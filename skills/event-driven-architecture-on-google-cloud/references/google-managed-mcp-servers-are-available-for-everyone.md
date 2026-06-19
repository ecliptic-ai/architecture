---
description: At Google Cloud Next ‘26, we announced Google-managed MCP servers are available for everyone, featuring 50+ MCP servers across general availability (GA) and preview (with more along the way)
title: Google-managed MCP servers are available for everyone | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/01_-_AI__Machine_Learning_H1ZyZG8.max-2600x2600.jpg
---

# 50+ fully managed MCP servers now available for Google Cloud services

April 28, 2026

##### Vidya Nagarajan

Director of Product Management, Google Cloud

##### Yubin Gong

Principal Engineer, Google Cloud

At Google Cloud Next ‘26, we announced that more than 50 [Google-managed Model Context Protocol (MCP) servers](https://docs.cloud.google.com/mcp/supported-products) are generally available or in preview, with more on the way.

**Why it matters:** To move beyond experimental prototypes, AI agents must be able to access real-world data and solve complex problems autonomously.

Google-managed Managed Context Protocol (MCP) servers provide the critical connectivity to bridge AI agents with the vast Google and Google Cloud ecosystems. By hosting these servers on an enterprise-ready, standardized platform, we eliminate the need to integrate with local MCP servers and offer a unified developer experience that’s integrated across major agent runtimes and frameworks.

### MCP built for the enterprise

Scaling your agent ecosystem shouldn't be a trade-off between speed and safety. You need the flexibility to grow, but you also need the guardrails to manage and govern your agents.

By pointing your AI agents toward Google-managed MCP endpoints, you’re plugging into the Google Cloud security stack, without needing to make regional configuration changes . While the platform offers deep flexibility for a range of architectures, here’s a snapshot of their capabilities that make it easier:

* **Strong interoperability:** Through protocol translation, your agents stay compliant with the [MCP specification](https://modelcontextprotocol.io/specification/), so that they play nice with public agents and agent frameworks like Gemini CLI, Claude, ChatGPT, VS Code, LangChain, Agent Development Kit (ADK), and CrewAI right out of the box. For example, we added support for [Resources and Prompts](https://modelcontextprotocol.io/specification/2025-11-25/server) as primitives in the MCP protocol in addition to Tools.
* **Centralized discovery:** No more hunting for MCP servers and tools. [Agent Registry](https://docs.cloud.google.com/agent-registry/overview) offers a unified directory where you can easily find and manage agents, MCP servers, and tools in one place.
* **Easy access with security and governance:** Every Google Cloud service is now MCP-enabled by default, allowing agents to easily communicate with Google Cloud. Leverage native [Cloud IAM](https://docs.cloud.google.com/mcp/control-mcp-use-iam#deny-all-mcp-tool-use) Deny policy for fine-grained access control.
* **Content safety:** With [Model Armor](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration) in-line integration, you can actively defend against indirect prompt injections and data exfiltration.
* **Full observability:** Maintain full oversight with OTel Tracing and [Cloud Audit Logs](https://docs.cloud.google.com/logging/docs/audit), helping you troubleshoot your agents, get comprehensive analytics, and perform forensic audits of agentic actions.

### Case study: Insta360 redefines video editing

[Insta360](https://www.insta360.com/), one of the world’s leading smart imaging brands, is leveraging the Google Cloud agentic ecosystem to redefine how users capture and share their lives. Building on its "Moments" feature, which delivers AI video highlights, the company developed an AI video editing agent using Google's Agent Development Kit, Agent Engine, A2A and Google-managed MCP servers, allowing users to complete video editing in the cloud through natural language input.

“Transitioning to managed MCP servers will allow us to move away from fragile point-to-point connections and toward a secure, scalable service-oriented architecture. By exposing our proprietary editing tools as managed endpoints, we're gaining the enterprise-grade stability needed to bring autonomous video creation to users around the world.” - Even Lin, Head of Cloud Services, Insta360

### Broad coverage across the Google ecosystem

Whether you’re automating internal operations or building customer-facing experiences, Google-managed MCP servers let your models do more than just chat; they provide the secure connections needed to take direct action across your Google Cloud services. Here are a few ways you can leverage them today:

### **1\. Infrastructure, operations, and security**

Agents can help move beyond simple monitoring to active orchestration, handling maintenance and monitoring while prioritizing critical security events. Here are a few examples of what this looks like in practice: 

* **Automated lifecycle management (ALM):** Provision and decommission resources dynamically based on real-time application demand using [GKE](https://docs.cloud.google.com/kubernetes-engine/docs/reference/mcp), [Cloud Run](https://docs.cloud.google.com/run/docs/reference/mcp), or [GCE](https://docs.cloud.google.com/compute/docs/reference/mcp) MCP servers.
* **Self-healing systems:** Empower agents to monitor events via [Cloud Logging](https://docs.cloud.google.com/logging/docs/reference/v2%5Fmcp/mcp) and [Monitoring](https://docs.cloud.google.com/monitoring/api/ref%5Fv3%5Fmcp/mcp), triggering recovery actions like traffic rerouting or deployment rollbacks before users are impacted.
* **Security orchestration:** Build sophisticated workflows that use [Google Security Operations](https://docs.cloud.google.com/chronicle/docs/reference/mcp) to automatically investigate and respond to emerging threats.
* **Fleet and network ops:** Use the [Android Management API](https://developers.google.com/android/management/use-android-management-mcp) MCP server to query device health in natural language, or utilize [Network Management API](https://docs.cloud.google.com/network-intelligence-center/docs/reference/networkmanagement/rest) MCP server to automate complex diagnostic workflows and surface actionable insights instantly.

### **2\. Databases, analytics, and storage**

To be effective, an agent must be grounded in [enterprise truth](https://cloud.google.com/transform/the-prompt-unlock-ai-agents-with-enterprise-truth?e=48754805) — the live, operational data residing in your production systems. This enables agents to interact with your data ecosystem with:

* **Real-time operational insights:** Connect agents directly to [Spanner](https://docs.cloud.google.com/spanner/docs/reference/mcp), [AlloyDB](https://docs.cloud.google.com/alloydb/docs/reference/mcp), [Cloud SQL](https://docs.cloud.google.com/sql/docs/mysql/reference/mcp), [Firestore](https://docs.cloud.google.com/firestore/docs/reference/mcp), or [Bigtable](https://docs.cloud.google.com/bigtable/docs/reference/admin/mcp) MCP servers to give agents access to both structured and unstructured operational data, to power immediate, data-driven decision-making.
* **Analytical insights and data pipelines:** Leverage [BigQuery](https://docs.cloud.google.com/bigquery/docs/reference/mcp) and [Managed Service for Apache Spark](https://docs.cloud.google.com/dataproc/docs/guides/use-dataproc-mcp) MCP servers to process large datasets, using [Pub/Sub](https://docs.cloud.google.com/pubsub/docs/use-pubsub-mcp) or [Managed Service for Apache Kafka](https://docs.cloud.google.com/managed-service-for-apache-kafka/docs/use-managed-service-for-apache-kafka-mcp) to trigger proactive system alerts.
* **Contextual retrieval:** Access both structured and unstructured data with [Google Cloud Storage](https://docs.cloud.google.com/storage/docs/reference/mcp) and [Knowledge Catalog](https://docs.cloud.google.com/dataplex/docs/mcp-overview) MCP servers to provide agents with real-time context required to accurately execute complex tasks.

### **3\. Services and apps**

Beyond raw data, agents require geographic context, technical documentation, and productivity tools to operate effectively. Here are ways agents can leverage these tools to perform more sophisticated and helpful tasks:

* **Developer assistance:** The [Developer Knowledge API](https://developers.google.com/knowledge/reference/mcp) MCP server grounds AI agents in Google's official developer documentation, allowing tools to reference up-to-date code samples and guides to solve complex technical issues as they happen.
* **Hyper-local contextual intelligence:** Use [Maps Grounding Lite](https://developers.google.com/maps/ai/grounding-lite/reference/mcp) MCP server to provide agents with trusted Google Maps data. Deliver high-precision responses for routing, weather, and local points of interest, minimizing hallucinations in travel and real-estate applications.
* **Conversational experience design:** The [Gemini Enterprise Agent Platform](https://docs.cloud.google.com/vertex-ai/docs/reference/mcp) MCP server empowers agents to act as AI supervisors, programmatically managing the full lifecycle of other models, prompts, and endpoints. Additionally, the [Customer Experience Agent Studio](https://docs.cloud.google.com/customer-engagement-ai/conversational-agents/ps/mcp-server) MCP server enables AI-assisted workflows for building, modifying, and maintaining customer experience agents.
* **Productivity and commerce**: Streamline team collaboration with Workspace MCP Servers for [Gmail](https://developers.google.com/workspace/gmail/api/guides/configure-mcp-server), [Drive](https://developers.google.com/workspace/drive/api/guides/configure-mcp-server), [Calendar](https://developers.google.com/workspace/calendar/api/guides/configure-mcp-server), [People API](https://developers.google.com/people/v1/configure-mcp-server), and [Chat](https://developers.google.com/workspace/chat/api/guides/configure-mcp-server) by allowing agents to summarize Gmail threads, draft Docs, manage Calendar invites and facilitate Google Chat workflows to streamline team productivity. Additionally, with [Google Pay and Wallet](https://developers.google.com/pay/api/web/reference/mcp) MCP server, you can integrate payments and digital passes into your agentic workflows, allowing AI to assist with onboarding, troubleshooting integrations, and monitoring checkout performance.

By unifying these capabilities with MCP, it’s easier than ever to build agents that don't just chat, but actually take actions on your behalf.

### **Technical scenario with demo**

To showcase the power of this platform, we built the Pet Passport demo using Google-managed MCP servers and ADK. 

This demo features an autonomous agent that handles a complete end-to-end workflow: planning the perfect pet-friendly day out in New York City. The agent implements a macro-to-micro reasoning chain:

1. First, it uses the BigQuery MCP server to analyze the NYC Dog License dataset and identify the specific neighborhood where a user's dog breed is most popular.
2. Next, it passes that context to the Google Maps MCP server to generate a verified walking route complete with dog parks and cafes.

When it's time to go live, Gemini CLI leverages the Cloud Run MCP server to deploy the agent application directly from a local machine, publishing the entire experience as a live, shareable URL.

Try it yourself with the [codelab](https://codelabs.developers.google.com/next26/build-adk-agent-google-mcps) or explore the source code on [GitHub](https://github.com/google/mcp/tree/main/examples/petpassport).

### **Start building today** 

We invite you to build production-grade workflows using the [Gemini CLI](https://geminicli.com/), [ADK](https://docs.cloud.google.com/gemini-enterprise-agent-platform/build/adk), or your preferred IDE.
