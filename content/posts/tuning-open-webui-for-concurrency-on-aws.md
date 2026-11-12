---
title: "Open WebUI Defaults Are Built for a Laptop: Tuning It for Real Concurrency on AWS"
date: 2026-06-16
draft: false
summary: "Every Open WebUI setting I changed to run it multi-pod on EKS with Amazon Bedrock, and why each one matters once real users show up."
tags: ["AWS", "Bedrock", "Open WebUI", "EKS", "GenAI", "Performance"]
---

Open WebUI is the fastest way I know to give a team a private, ChatGPT-style interface over Amazon Bedrock. Run it on your laptop and it just works. Put it behind a load balancer with a few hundred people hitting it at once and it falls over in ways that aren't obvious from the docs.

The reason is simple: the defaults assume one process, one user, one machine. Local SQLite-backed vector store. Files on local disk. A 40-thread pool. An LLM call every time you pause typing.

I built a reference deployment on EKS (Open WebUI, LiteLLM as the OpenAI-compatible proxy, Aurora PostgreSQL with pgvector, ElastiCache, S3, all Terraform) and kept a running list of every environment variable I changed from the default. Here's that list, grouped by the problem each change solves.

## Problem 1: Multiple pods have to share state

The moment you run more than one replica, anything stored in a single pod's memory or disk becomes a bug. A user's websocket lands on pod A, their next request lands on pod B, and things get weird.

| Variable | Default | Set to | Why |
|---|---|---|---|
| `WEBSOCKET_MANAGER` | none | `redis` | Websocket state shared across pods |
| `REDIS_CLUSTER` / `WEBSOCKET_REDIS_CLUSTER` | `false` | `true` | ElastiCache Serverless is a cluster; without these, connections fail |
| `VECTOR_DB` | `chroma` | `pgvector` | Chroma is SQLite-backed and not safe across pods |
| `STORAGE_PROVIDER` | `local` | `s3` | An upload on one pod is invisible to the others otherwise |
| `WEBUI_SECRET_KEY` | generated | shared K8s secret | Every pod must sign and verify the same JWTs |

The `REDIS_CLUSTER` one cost me the most time. The error doesn't tell you "you're talking to a cluster." It just fails.

## Problem 2: Concurrency limits you don't know you have

| Variable | Default | Set to | Why |
|---|---|---|---|
| `THREAD_POOL_SIZE` | 40 threads | `2000` | The default pool exhausts quickly and the app stalls |
| `UVICORN_WORKERS` | `1` | `1` | Left alone on purpose: scale with pod replicas, not workers per pod |
| `CHAT_RESPONSE_STREAM_DELTA_CHUNK_SIZE` | `1` | `7` | Send 7 tokens per SSE event instead of 1 |
| `ENABLE_CHAT_RESPONSE_BASE64_IMAGE_URL_CONVERSION` | `false` | `true` | Move big base64 images to S3 URLs instead of shipping them through Redis and the DB |

The thread pool is the big one. Open WebUI does a lot of blocking work in that pool, and 40 threads disappear fast when everyone is streaming at once. The symptom looks like the model is slow. It isn't. The app is queuing behind its own threads.

Streaming one token per event is lovely on a laptop. At scale it's a lot of tiny events for every pod and every browser to process. Batching 7 at a time is invisible to users and noticeably cheaper on CPU.

## Problem 3: The database connection pool

| Variable | Default | Set to | Why |
|---|---|---|---|
| `DATABASE_POOL_SIZE` | SQLAlchemy default | `10` | Explicit per-pod pool, no unbounded growth |
| `DATABASE_POOL_MAX_OVERFLOW` | `0` | `20` | Headroom for spikes before requests queue |
| `DATABASE_ENABLE_SESSION_SHARING` | `false` | `true` | Reuse sessions across requests |
| `ENABLE_REALTIME_CHAT_SAVE` | `false` | `false` | Left off on purpose: on writes every token to the database |

Do the math before you set the pool: pods times (pool size plus overflow) has to fit inside what your Aurora capacity allows. Autoscaling the app tier without thinking about this just moves the bottleneck to the database.

## Problem 4: Background LLM calls you're paying for

Open WebUI quietly makes a lot of extra model calls. On a laptop you never notice. Across a whole company it's real money and real quota.

| Variable | Default | Set to | Why |
|---|---|---|---|
| `ENABLE_AUTOCOMPLETE_GENERATION` | `true` | `false` | Fires a model call on every pause while typing |
| `ENABLE_FOLLOW_UP_GENERATION` | `true` | `false` | One extra call per response |
| `ENABLE_TAGS_GENERATION` | `true` | `false` | One extra call per new chat |
| `TASK_MODEL` / `TASK_MODEL_EXTERNAL` | main model | Claude Haiku | Titles and query generation don't need your biggest model |
| `ENABLE_BASE_MODELS_CACHE` + `MODELS_CACHE_TTL` | off / `1` | on / `300` | Stop polling the proxy for the model list every second |

The task model is the one I'd change first on any deployment. Nobody needs Opus to write a chat title.

## Problem 5: RAG that's kind to your quota

| Variable | Default | Set to | Why |
|---|---|---|---|
| `RAG_EMBEDDING_BATCH_SIZE` | `1` | `100` | 100 chunks per embedding call instead of 1 |
| `RAG_EMBEDDING_CONCURRENT_REQUESTS` | unlimited | `100` | Cap per pod so a big upload can't eat your Bedrock quota |
| `RAG_SYSTEM_CONTEXT` | `false` | `true` | Put retrieved context in the system message so prompt caching can kick in |
| `RAG_WEB_SEARCH_BYPASS_EMBEDDING` | `false` | `true` | Don't embed throwaway web results |
| `CHUNK_SIZE` / `CHUNK_OVERLAP` | 1000 / 100 | 1600 / 200 | More context per chunk, fewer split sentences |
| `RAG_TOP_K` | `3` | `10` | Give the reranker more to work with |

The concurrency cap is about quota math: pods times concurrent requests should stay under your Bedrock requests-per-minute limit with room left for chat traffic. Check your account's actual quotas in Service Quotas rather than trusting a number in a blog post (including this one).

`RAG_SYSTEM_CONTEXT` is my favorite small change. Moving retrieved context into the system message keeps the front of the prompt stable, which is what prompt caching needs to work. Lower latency and lower token cost for one boolean.

## Problem 6: Timeouts that assume short answers

Long Opus responses and big document jobs blow past the default 300-second client timeout. I raised `AIOHTTP_CLIENT_TIMEOUT` to 1800 and bumped the model-list timeouts a little to survive proxy cold starts.

## The takeaway

None of these are exotic. They're all documented somewhere. The trouble is that the defaults are tuned for the first five minutes of using the product, and nothing tells you which ones bite when you scale. If you're putting Open WebUI in front of Bedrock for more than a handful of people:

1. Make every piece of state shared before you add a second pod.
2. Raise the thread pool before you blame the model.
3. Turn off the background LLM features you don't need, and route the rest to a small model.
4. Do the quota and connection-pool math up front.
5. Load test with streaming responses, not just page loads.

Next, I want to see how much of the proxy layer I can remove entirely.
