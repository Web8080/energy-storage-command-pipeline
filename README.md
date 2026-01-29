# Allye MAX300 Remote Control & Scheduling System

Design for day-ahead charge/discharge schedules: cloud ingestion, MQTT delivery to Raspberry Pi agents, and observability. Allye take-home.

**Start here:** [DESIGN_PROPOSAL.md](./DESIGN_PROPOSAL.md) — design doc (architecture, protocols, message format, cloud services, scaling). Diagram and message examples are in the doc.

**Repo layout:**

| Path | Contents |
|------|----------|
| [DESIGN_PROPOSAL.md](./DESIGN_PROPOSAL.md) | Design document (2-page target). Diagram embedded in Section 4. |
| [docs/allye-max300-architecture-diagram.png](./docs/allye-max300-architecture-diagram.png) | Architecture diagram image. |
| [architecture-diagram.md](./architecture-diagram.md) | Mermaid source and Excalidraw box list. |
| [message-schemas/](./message-schemas/) | JSON schemas (schedule command, ack, execution report, heartbeat) and [examples.md](./message-schemas/examples.md). |

Victor Ibhafidon · 29/01/2026
