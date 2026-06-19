---
name: event-driven-architecture-on-google-cloud
description: >-
  A short guide to one shape of application. A user submits data, a few steps run on it — often a Gemini call among them — and a result returns to the browser. The work is orchestration, not heavy computation. Every choice below favors three properties — decoupled, stateless, event-driven — and names the service that does each job.
---

# Event-Driven Serverless Architecture on Google Cloud

A short guide to one shape of application: a user submits data, a few steps run on it — often a Gemini call among them — and a result returns to the browser. The work is orchestration, not heavy computation. Every choice below favors three properties — decoupled, stateless, event-driven — and names the service that does each job.

## The shape

Keep three concerns apart: what moves the work, what runs it, and where state lives. Confusing them is the usual cause of tangled designs.

Events move the work. Serverless compute runs it and holds nothing between requests. Managed services hold the state. Compute that persists nothing locally can scale to zero, scale out, and survive a restart without losing a request.

## What each service is for

Workflows is the orchestrator. It owns the order of steps, calls each service, retries on failure, and can run for up to a year. Its definition is the single source of truth for what happens and in what order. It holds the orchestration state so the workers don't have to.

Cloud Run runs the code. A service answers HTTP and scales to zero. A job runs to completion for batch work. A worker pool pulls from a queue but does not autoscale on its own. Cloud Run functions are the same engine wrapped around a single trigger. Treat any of them as a stateless worker the workflow calls.

Gemini runs through Vertex AI. Call it from a workflow with the Vertex AI connector, or from Cloud Run with the service account's own credentials and no API keys. Inside an orchestration the workflow calls it directly; reach for Cloud Run only when the logic around the call outgrows the workflow language.

Pub/Sub is the asynchronous bus. Publish once, and every subscriber gets its own copy. Use it to fan out to many consumers, to absorb traffic spikes, and to carry your own domain events between services. You already use it indirectly, because Eventarc runs on top of it.

Eventarc turns a change in a Google Cloud service into a trigger: when a document is created, start this workflow. It delivers a standard event and manages the Pub/Sub plumbing for you. Use Eventarc for a managed trigger off a Google Cloud source; drop to raw Pub/Sub when you need fan-out, buffering, or your own events.

Cloud Tasks dispatches individual units of work to one endpoint, with per-task scheduling and a queue rate limit. Use it to defer and throttle, where Pub/Sub broadcasts.

Firestore is the real-time bridge to the browser. It is a document database — collections of JSON-like records — that pushes changes to subscribed clients. The browser opens one listener on a document and receives every later update. Neither Redis nor Postgres can do this; pushing a change straight to a browser is Firestore's distinct job.

Cloud SQL for Postgres, or AlloyDB, is the relational system of record. Reach for it when the data is relational and transactional: joins, constraints, billing, audit. It sits behind the workflow, never in front of the browser. BigQuery handles heavy analytics. Memorystore for Redis holds ephemeral state — cache, sessions, idempotency keys — and can lose acknowledged writes during a failover, so keep nothing there that must survive.

## Returning a result to the browser

A browser receives data three ways: an HTTP response, a connection held open, or a real-time database listener. Pub/Sub and Eventarc move events between back-end services; neither reaches a browser. The return path is therefore a separate concern from the orchestration.

Prefer the asynchronous pattern. The browser writes a request document to Firestore. Eventarc sees the new document and starts the workflow, passing the event in as an argument. The workflow runs its steps, calling Gemini and anything else, and writes status and result to a second document through the Firestore connector. The browser, already listening on that document, renders each update as it arrives. No polling, and no connection held open for the length of the job.

Write the request to one collection and the result to another. If the trigger watched the collection the workflow writes to, the workflow's own writes would fire it again without end.

One service owns the orchestration, and it is the workflow. The workflow writes its own progress to Firestore as a projection the browser observes; nothing else tracks the job. A synchronous design splits that truth — it pins a Cloud Run instance to poll the workflow and tracks state in two places at once. Avoid it.

## Authentication and authorization

Two identity planes run here, and they never mix.

Users are one plane. They sign in with Firebase Authentication, or with Identity Platform when you need single sign-on, multi-factor, or multi-tenancy — the same engine with enterprise features added. Sign-in returns a signed token carrying the user's ID and any custom claims. Firestore Security Rules then decide what each user may touch: a rule compares the token's user ID against the document's owner, so a user reads only their own jobs. For direct database access this is the whole user-authorization layer, and it needs no server code.

Services are the other plane. Each component runs as a service account holding IAM roles — the workflow may call Vertex AI, invoke Cloud Run, and publish to Pub/Sub. The tokens flow automatically: OAuth for Google APIs, OIDC for Cloud Run.

The two planes meet at Firestore, and one rule governs the seam: the back end bypasses Security Rules. A server library authenticates with its own credentials and ignores the rules entirely. Rules guard the browser's direct access; IAM guards everything the back end does. So the workflow can write a result the user is only allowed to read, and the user cannot forge it.

## When you don't need a service

Most of this is additive. The basic flow — submit, orchestrate, return — needs only Firebase Authentication, Firestore, Eventarc, Workflows, and Gemini. Add Pub/Sub when you need fan-out or buffering. Add Postgres when the data turns relational. Add Cloud Tasks when you must throttle. Add a Cloud Run worker when a step outgrows the workflow language. Add each one when its problem appears, and not before.

## Reference

See [the reference guide](references/REFERENCE.md) for real-world examples of building with Google Cloud.
