---
description: Learn how to use A2UI, an open protocol for agent-driven user interfaces, and integrate an A2UI-enabled agent with Gemini Enterprise so your agent renders rich and interactive UI natively in the GE chat surface.
title: Guide to Gemini Enterprise and A2UI integration | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/11_-_Developers__Practitioners_a4Y5EGr.max-2600x2600.jpg
---

# Developer's guide to Gemini Enterprise and A2UI integration

May 29, 2026

##### Dave Wang

Forward Deployed Engineer, Google Cloud

##### Yuan Tian

Software Engineer, Google Cloud AI

If you've built a chatbot, you know this conversation:

**User:** "Book a table for two tomorrow at 7pm." **Agent:** "Okay, for what day?" **User:** "Tomorrow." **Agent:** "What time?"

A date picker would have ended this in one tap. But until recently, agents had no standard way to render a date picker — or a map, or a multi-select list — inside the chat surface they live in. They could only return text or markdown for generic usage.

Today, we're walking through how to fix that with **A2UI**, an open protocol for agent-driven user interfaces, and how to integrate an A2UI-enabled agent with **Gemini Enterprise (GE)** so your agent renders rich and interactive UI natively in the GE chat surface — and in your own custom frontend if you want one. We'll use a working restaurant-finder agent — built with the Google Agent Development Kit (ADK), the A2A protocol, and Gemini — as the reference. The full source is on [GitHub](https://github.com/wadave/agent-a2ui-demo) and there's a [2-minute demo video.](https://youtu.be/%5F5AaYwyqVio)

### The problem: agents speak text, but users want UI

Most agent frameworks today return strings. That's fine for short answers, but it breaks down quickly:

- **Multi-turn slot filling** (date, time, party size) burns turns and patience.
- **Choices among options** (which restaurant? which insurance plan?) become long bulleted lists the user has to copy-paste back.
- **Spatial information** (locations, routes, floor plans) is reduced to addresses.

Developers have tried to patch this by sending HTML or JavaScript fragments, but that introduces real risks: cross-site scripting, UI injection from a remote agent you don't fully control, and visual drift from the host app's design system. What's needed is a way to transmit UI that's **safe like data and expressive like code**.

### What A2UI is

[A2UI](https://a2ui.org/) is an open protocol, [introduced by Google](https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/) and co-developed with the Flutter team and product teams behind Gemini Enterprise. Instead of returning text or HTML, an agent returns a JSON payload that describes a UI: a tree of **components** (Card, Text, Button, ChoicePicker, Image, …) and a separate **data model** holding the values those components display.

Three properties make this useful in practice:

- **Declarative, not executable.** The payload is data. The client only renders components from a pre-approved **catalog**, so a remote agent can't inject arbitrary code or steal credentials through a UI widget.
- **Streaming-friendly.** The format is a flat list of small JSON messages, so the LLM can emit them incrementally and the client can paint as they arrive.
- **Framework-agnostic.** The same agent response renders through Lit, Angular, Flutter, or native mobile. The agent doesn't know — or care — what's on the other end.

A2UI is also **transport-agnostic**. The messages ride inside whatever pipe you already use: A2A JSON-RPC, AG-UI, WebSockets, SSE. In our reference implementation, A2UI rides inside the [A2A protocol](https://a2aprotocol.ai/) as DataPart objects with the MIME type application/json+a2ui.

### Where A2UI sits in the stack

A2UI is one piece of a four-layer stack. Confusion usually comes from conflating these layers — they're each doing a different job:

| **Layer**                 | **Owns**                                                                      | **Examples**          |
| ------------------------- | ----------------------------------------------------------------------------- | --------------------- |
| **App experience**        | Client shell and conversation state — chat window, input box, message history | CopilotKit, AG-UI     |
| **Pixel drawing**         | Turning component descriptions into actual rendered UI                        | Lit, Flutter, Angular |
| **Conversation pipeline** | Client–server transport — sending messages, receiving responses               | A2A Protocol          |
| **Cargo (data format)**   | The thing flowing through the pipeline that describes the UI                  | A2UI                  |

Read top to bottom: **CopilotKit/AG-UI** owns the app experience. **Lit/Flutter/Angular** own the rendering. While **CopilotKit** and **AG-UI** provide valuable abstractions, they remain strictly optional for implementing **A2UI**. In this architecture, **A2A** serves as the underlying conversation pipeline, while **A2UI** represents the structured cargo that actually traverses that pipe.

That separation is why the same A2UI payload renders identically in three very different deployment shapes:

- **Bespoke web app** — a custom client shell (like the reference repo's Lit frontend/) plus a custom A2UI renderer.
- **CopilotKit / AG-UI app** — CopilotKit owns the chat shell, an A2UI renderer is registered inside it for rich cards.
- **Gemini Enterprise** — GE is the shell, the renderer, and the transport client. You only build the agent.

So for the GE path, the stack collapses to two layers you control: the **A2A endpoint** (your agent) and the **A2UI cargo** it emits. The other two layers are GE's responsibility. CopilotKit and AG-UI are great if you're building a standalone product UI elsewhere — they're just out of scope for embedding an agent inside Gemini Enterprise.

### Pattern revisions

The protocol evolves quickly, and different clients support different revisions. Two patterns are common today:

- **Inline pattern** — the agent sends a component tree with the data baked into each component (the pattern Gemini Enterprise renders today).
- **Decoupled pattern** — the agent sends the component tree and the data model as separate messages, so subsequent turns can update one without re-sending the other. This reduces tokens and latency for long-running conversations and is the direction the protocol is heading.

The reference repo serves **both** patterns from one backend, picking which to emit per request based on the client's X-A2A-Extensions header. As new revisions ship, you add another catalog and the same negotiation pattern keeps working.

### How A2UI works inside Gemini Enterprise

Gemini Enterprise ships with a built-in A2UI renderer. For the developer, that means the integration story is short:

1. **Build your A2A agent**, embedding an A2UI catalog and example payloads alongside the regular tool definitions.
2. **Register the agent** with Gemini Enterprise as an A2A endpoint. (Use make register-gemini-enterprise in the reference repo.)
3. **A GE admin shares the agent** with employees, just like any other agent in the GE catalog.

At runtime, the flow looks like this:

1. The user types a request in the GE chat. GE calls your agent's A2A endpoint and sends along **GE's own A2UI catalog** — the list of UI components GE knows how to render.
2. Your agent decides whether a UI widget is the right response. If yes, it emits an A2UI JSON message (e.g., a ChoicePicker of restaurant options). If no, it falls back to text. Both can coexist in the same response.
3. GE receives the JSON, validates it against its catalog, and renders the widget natively in **GE's own design language** — so it visually matches the rest of the chat surface.
4. When the user interacts with the widget (selects three options, picks a date), GE serializes the interaction back into JSON and sends it to your agent as the next turn. Your agent processes structured input, not free-form text.

One thing worth flagging: because your agent doesn't ship its own renderer for GE, you don't need to choose a frontend framework to start. Your A2A endpoint can run anywhere — Cloud Run, GKE, on-prem — and GE handles the rendering.

### High-level architecture example

The reference implementation is an ADK backend on Cloud Run designed to plug seamlessly into Gemini Enterprise.

- Gemini Enterprise connects directly to your agent using standard A2A JSON-RPC calls.
- The agent serves the inline message pattern expected by the Gemini Enterprise managed UI.
- Custom components like GoogleMap render via Google Maps Embed iframes, with the API key injected server-side so the LLM never sees it.

The following demonstration illustrates how Google Maps functions as a live, interactive component within Gemini Enterprise rather than a static image. Leveraging A2UI's streaming-friendly architecture, the agent updates the map view in real-time—dropping pins and adjusting coordinates incrementally as results arrive from the Maps API.

### See it running, then build your own

- **Detailed implementation guide** [here.](https://github.com/wadave/agent-a2ui-demo/blob/main/docs/implementation%5Fdetails%5Fguide.md)
- **Demo video** (2 minutes, end-to-end with both the Lit shell and Gemini Enterprise): <https://youtu.be/%5F5AaYwyqVio>
- **A2UI spec and component reference**: [a2ui.org](https://a2ui.org/)
- **Gemini Enterprise updates**, including the A2UI renderer: [What's new in Gemini Enterprise](https://cloud.google.com/blog/products/ai-machine-learning/whats-new-in-gemini-enterprise)
- **A2UI generative UI announcement**: [Introducing A2UI generative UI](https://developers.googleblog.com/a2ui-v0-9-generative-ui/)

If you're already building agents on Google Cloud, the fastest path is to clone the reference repo, run make local-backend for a local smoke test, and then make register-gemini-enterprise to wire it into GE. From there, swap in your own catalog, your own tools, and your own domain. The next time a user asks your agent for "a table for two tomorrow at 7pm," the answer can be a date picker instead of another question.
