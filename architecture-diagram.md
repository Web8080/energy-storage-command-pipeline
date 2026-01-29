# Allye MAX300 System Architecture

Diagram source. The design doc ([DESIGN_PROPOSAL.md](./DESIGN_PROPOSAL.md)) embeds the flowchart and sequence diagram in Section 4.

---

## High-Level Flow (Left to Right)

```mermaid
flowchart LR
    subgraph external["External"]
        A[Schedule Provider\nHTTP GET /tomorrow]
    end

    subgraph cloud["Cloud"]
        B[Schedule Ingestion\nFastAPI on Lambda]
        subgraph storage["Schedule Storage"]
            C1[(S3\nRaw schedules / audit)]
            C2[(DynamoDB\nCommand state & metadata)]
        end
        D[AWS IoT Core\nMQTT over TLS]
    end

    subgraph edge["Edge"]
        E[Raspberry Pi\nMAX300 Python Agent]
        F[Battery Controller\nCharge / Discharge]
    end

    A --> B
    B --> C1
    B --> C2
    B --> D
    D --> E
    E --> F

    E -.->|ACK, execution reports, heartbeats| D
    D -.->|Logs & metrics| G[CloudWatch]
```

---

## Component Boxes (for Excalidraw)

Use these as labels when drawing in Excalidraw.

### Box 1: Schedule Provider
- **Label:** External Schedule API
- **Notes:** HTTP GET `/tomorrow`; returns JSON schedule for next day.

### Box 2: Schedule Ingestion Service
- **Label:** FastAPI (AWS Lambda)
- **Responsibilities:** Fetch schedule; validate JSON; normalize intervals; generate command messages; publish to IoT Core.

### Box 3: Schedule Storage
- **S3:** Raw schedules and audit logs.
- **DynamoDB:** Command state, metadata, acknowledgements, execution results.

### Box 4: IoT Messaging Layer
- **Label:** AWS IoT Core (MQTT over TLS)
- **Notes:** Per-device topics; QoS enabled; certificate-based auth.

### Box 5: Raspberry Pi (Edge Device)
- **Label:** MAX300 Python Agent
- **Sub-components:** MQTT Client; Schedule Validator; Local Store (SQLite); Execution Engine; Telemetry Reporter.

### Box 6: Battery Controller
- **Label:** Charge / Discharge Interface
- **Notes:** Hardware interaction layer (mode + rate_kw).

**Feedback (dashed arrows):** Raspberry Pi to IoT Core: ACK messages, execution reports, heartbeats. IoT Core / Lambda to CloudWatch: logs and metrics.

---

## Command Flow (Sequence)

```mermaid
sequenceDiagram
    participant SP as Schedule Provider
    participant API as FastAPI Lambda
    participant S3 as S3
    participant DDB as DynamoDB
    participant IoT as IoT Core
    participant Pi as Raspberry Pi
    participant Bat as Battery Controller

    SP->>API: GET /tomorrow (schedule)
    API->>API: Validate, normalize
    API->>S3: Store raw schedule
    API->>DDB: CREATED (command state)
    API->>IoT: Publish command (QoS 1)
    API->>DDB: SENT

    IoT->>Pi: Command payload
    Pi->>Pi: Validate (48 intervals)
    Pi->>Pi: Persist SQLite
    Pi->>IoT: ACK (message_id)
    IoT->>API: (via rule / Lambda) ack received
    API->>DDB: ACKNOWLEDGED

    loop Every half-hour
        Pi->>Bat: Set mode + rate_kw
        Bat-->>Pi: OK / FAIL
        Pi->>IoT: Execution report
        API->>DDB: EXECUTED | FAILED
    end
```

---

## Observability Flow

```mermaid
flowchart LR
    subgraph sources["Sources"]
        L1[Lambda logs]
        L2[IoT Core logs]
        L3[Pi agent logs]
    end

    subgraph observability["Observability"]
        CW[CloudWatch Logs]
        MX[CloudWatch Metrics]
        XR[X-Ray traces]
    end

    L1 --> CW
    L2 --> CW
    L3 --> CW
    L1 --> MX
    L2 --> MX
    L1 --> XR
```

All logs and traces are keyed by `message_id` and `device_id` for cross-filtering when debugging at scale.
