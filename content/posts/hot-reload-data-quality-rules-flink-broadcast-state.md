---
title: "Data Quality Rules You Can Change Without Restarting the Stream"
date: 2026-07-21
draft: false
summary: "A metadata-driven pattern for real-time telemetry quality: rules as versioned config, compiled in a control plane, and hot-reloaded into Apache Flink with broadcast state."
tags: ["AWS", "Apache Flink", "Kafka", "Streaming", "Data Quality", "IoT"]
---

Most streaming pipelines check one thing at ingestion: does this message match the schema? If it parses, it flows. Then a stuck sensor reports the same temperature for six hours, a device replays yesterday's batch, a firmware update changes units from kPa to psi, and all of it lands in the lake, the dashboards and the ML features before anyone notices.

I've been prototyping a better answer for connected-device telemetry: a data quality engine that sits at the point of ingestion and decides, for every message, whether it's trusted, suspect or broken. The part I want to write about is how the rules get there, because that's where most designs I've seen fall short.

## The usual approach and its problem

The common pattern is to keep rules in a file in S3 and have the stream processor poll or cache it with a TTL. It works, mostly. But:

- Reloads are lazy. Different parallel workers pick up the new rules at different times, so for a while half your fleet is judged by v11 and half by v12.
- Anything more than a threshold change usually means code, which means a redeploy, which means a restart.
- Nobody can tell you exactly which rule version scored a given record.

For data that feeds operational decisions, "mostly consistent" isn't good enough.

## Two planes: control and execution

The design splits the problem in two.

![Control plane and execution plane. In the control plane, definitions in Git (rules, mappings, reference data) are validated, compiled and published as a versioned rule package to a Kafka metadata topic. In the execution plane, Apache Flink harmonizes raw telemetry into canonical records, then a quality operator keyed per device validates, detects flatlines, duplicates and drift, repairs and scores each record. The metadata topic is broadcast to both operators, so new rules apply with no restart and no lost state. Every record exits to curated, quarantine or dead-letter.](/images/posts/dq-control-execution-planes.svg)

**The control plane** is where a data steward defines what good data looks like: the canonical model, how each source maps into it, quality rules, repair rules, reference ranges and allowed values. All of it is YAML in Git. A compiler validates the definitions, versions them and publishes a package to a Kafka metadata topic.

**The execution plane** is Apache Flink. A harmonization operator turns each provider's shape into one canonical record. A quality operator, keyed by device, validates, detects patterns, repairs where a rule allows it, scores the record and routes it.

## Broadcast state is the trick

Flink has a feature built for exactly this: the [broadcast state pattern](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/fault-tolerance/broadcast_state/). You connect a low-volume control stream (the rule packages) to your high-volume data stream. Every parallel subtask receives every rule update, stores it in broadcast state, and applies it to the next record it processes.

What that buys you:

- **No restart.** A steward publishes a new rule version and running operators absorb it in seconds.
- **No lost state.** Per-device history (the thing you need to detect a flatline or slow drift) survives the update.
- **Consistency.** Every subtask gets the same update through the same stream, instead of each one polling on its own schedule.
- **Lineage.** Each output record can carry the rule package version that scored it.

Code changes are a different story, and that's fine. When operator logic changes, take a savepoint, deploy the new job and restore from it. Rule changes, which happen far more often, never need that.

## What a rule looks like

Here's an illustrative rule (simplified, not from any real deployment):

```yaml
id: coolant-temp-range
version: 3
signal: Vehicle.Powertrain.CombustionEngine.ECT
check: range
bounds_from: reference.engine_model.coolant_temp   # never hard-coded
on_fail:
  severity: warning
  action: quarantine
```

Two details matter. Bounds come from reference data, keyed by device model, not from numbers in code. And the action is part of the rule: flag it, repair it in flight where that's safe, or quarantine it.

## Three exits, always

Every record leaves on exactly one of three streams:

- **Curated**: trusted, scored, canonical. Goes to real-time consumers and the lake.
- **Quarantine**: parsed fine but failed quality checks. Held for review or repair.
- **Dead-letter**: couldn't be processed at all.

One nuance I liked: if a single signal can't be extracted (a missing field, a type mismatch), the record is emitted without that field and the omission lowers its quality score. That's a quality signal, not a routing failure. Dead-lettering a whole message because one field is missing throws away good data.

## Watch the ratios, not just the lag

Streaming dashboards usually track throughput and consumer lag. Here, the primary health signal is the **quarantine and dead-letter ratio** per source. Custom CloudWatch metrics carry those ratios, plus per-rule hit counts, and alarms fire when a ratio crosses a threshold. A sudden jump in one provider's quarantine rate tells you about a firmware change or a broken mapping before anyone downstream files a ticket.

## A few other choices worth stealing

- **A canonical model based on an open standard.** I aligned signals to [COVESA VSS](https://covesa.github.io/vehicle_signal_specification/) instead of inventing a schema. Mappings from each source get a lot easier to review.
- **Regional stacks that never share telemetry.** Each region runs independently; only compiled, non-personal rule packages replicate. Data residency is part of the structure, not left to a policy document.
- **The simulator can't touch production.** A synthetic telemetry generator with bad-data injection lives in the same repo, but CI enforces that the production data plane can never import it.
- **PyFlink, on purpose.** Keeping the whole stack in Python makes it maintainable for a Python-fluent data team. Broadcast state, keyed state, event time, exactly-once and savepoints all work in Flink's Python DataStream API. If the Python serialization boundary ever becomes the bottleneck, isolate the hottest operator.

## The takeaway

Treat data quality rules like deployable artifacts: versioned, validated, compiled and shipped, with the stream processor built to accept them live. Once rules are config rather than code, the people who know what good data looks like can change them without opening a ticket with the platform team.
