# message-schemas

JSON schemas for cloud–device messages. Timestamps ISO8601; all payloads include `schema_version`.

| File | Purpose |
|------|---------|
| `schedule-command.schema.json` | Cloud → device: day-ahead schedule (48 half-hour intervals) |
| `ack.schema.json` | Device → cloud: command ACK (ACCEPTED / REJECTED) |
| `execution-report.schema.json` | Device → cloud: interval result (EXECUTED / FAILED / SKIPPED) |
| `heartbeat.schema.json` | Device → cloud: liveness |

Provider API `mode`: 1 = discharge, 2 = charge. Command schema uses `"DISCHARGE"` and `"CHARGE"`; ingestion maps 1→DISCHARGE, 2→CHARGE.

Payload examples: [examples.md](./examples.md).
