# System Design Repository Agent Guidelines

This file defines the operating procedures for AI agents assisting in this repository.

## 🎯 Purpose
The user is studying System Design. Their workflow is:
1. They paste raw, unformatted notes into `dump.txt`.
2. They ask the agent to process/convert `dump.txt`.
3. The agent transforms the raw dump into a polished, production-grade Markdown note with Mermaid diagrams, trade-off comparisons, real-world examples, and interview takeaways.
4. The agent updates `README.md` to link to the new note and mark progress.
5. The user subsequently overwrites `dump.txt` for their next study session.

---

## 🛠️ Step-by-Step Processing Protocol for `dump.txt`

Whenever the user asks to process `dump.txt`:

1. **Read & Extract Topic:**
   - Read `dump.txt`.
   - Identify the core subject, key concepts, technical trade-offs, and details mentioned.
   - If the topic spans multiple major distinct subjects, propose splitting them or organize into logical subsections.

2. **Determine Target Folder & Filename:**
   Map the topic into the appropriate directory:
   - `00-framework/`: Interview steps, math/estimation cheat-sheets, latency charts.
   - `01-networking-and-communication/`: OSI, DNS, HTTP/2/3, WebSockets, gRPC, proxies.
   - `02-scalability-and-traffic/`: Load balancers, API gateways, rate limiting.
   - `03-caching-and-cdn/`: Caching patterns, Redis, Memcached, CDNs.
   - `04-databases-and-storage/`: SQL vs NoSQL, sharding, replication, B-trees, LSM-trees.
   - `05-distributed-systems/`: CAP, PACELC, consensus (Raft/Paxos), 2PC, Saga, locks.
   - `06-messaging-and-events/`: Kafka, RabbitMQ, SQS, event-driven, stream vs batch.
   - `07-reliability-and-observability/`: Circuit breakers, telemetry, SLA/SLO.
   - `08-case-studies/`: Specific designs (TinyURL, Twitter feed, Uber geospatial, etc.).

   Use lowercase kebab-case for filenames, e.g. `consistent-hashing.md`, `rate-limiting-algorithms.md`.

3. **Format & Structure Standards:**
   Every note **must** be comprehensive and follow this layout:
   - **Title & Metadata:** Topic name, quick 2-line executive summary / TL;DR.
   - **Why & Problem Statement:** What problem does this solve in large-scale systems? What fails without it?
   - **Core Architecture & Flow (Mermaid Diagram):**
     - Use valid Mermaid syntax (`flowchart TD`, `flowchart LR`, `sequenceDiagram`, `stateDiagram-v2`, `erDiagram`).
     - Always quote labels with special characters like parentheses `["Node (Details)"]`.
     - Include clear data flow and step-by-step numbers if relevant.
   - **Mechanics & Deep-Dive:** Detailed explanation of the internals, formulas, algorithms, or mechanics (e.g. hash ring virtual nodes, token replenishment rate, B-Tree splitting).
   - **Trade-Offs & Comparison Matrix:** Markdown table comparing alternatives (e.g. Token Bucket vs Leaky Bucket; Strong vs Eventual consistency).
   - **Real-World Implementations:** How top companies use it (e.g. Discord using Cassandra -> ScyllaDB, Amazon DynamoDB hash rings, Cloudflare Anycast).
   - **Interview Tips & Pitfalls:** Common interviewer questions, gotchas, edge cases, failure scenarios.

4. **Update `README.md`:**
   - Find the matching topic in `README.md`.
   - Add/update the link to the generated markdown file.
   - Mark the checkbox from `[ ]` to `[x]`.

5. **Do NOT delete the target note when `dump.txt` is updated:**
   - The user will overwrite `dump.txt` for future dumps. Keep all compiled `.md` files safe in their respective folders.
