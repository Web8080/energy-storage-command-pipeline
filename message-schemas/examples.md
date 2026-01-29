# Message Format Examples

## 1. Schedule command (cloud to device)

Full day: 48 intervals; only first and last intervals shown.

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "device_id": "allye-max300-001",
  "schedule_date": "2025-12-25",
  "intervals": [
    {
      "start_time": "2025-12-25T00:00:00Z",
      "end_time": "2025-12-25T00:30:00Z",
      "mode": "DISCHARGE",
      "rate_kw": 100
    },
    {
      "start_time": "2025-12-25T00:30:00Z",
      "end_time": "2025-12-25T01:00:00Z",
      "mode": "CHARGE",
      "rate_kw": 10
    },
    "... 46 more intervals ...",
    {
      "start_time": "2025-12-25T23:30:00Z",
      "end_time": "2025-12-26T00:00:00Z",
      "mode": "DISCHARGE",
      "rate_kw": 30
    }
  ],
  "issued_at": "2025-12-24T12:01:00Z",
  "schema_version": "v1"
}
```

## 2. Acknowledgement (device to cloud)

Accepted:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "device_id": "allye-max300-001",
  "ack_at": "2025-12-24T12:01:05Z",
  "status": "ACCEPTED",
  "schema_version": "v1"
}
```

Rejected (e.g. wrong interval count):

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "device_id": "allye-max300-001",
  "ack_at": "2025-12-24T12:01:05Z",
  "status": "REJECTED",
  "reject_reason": "Expected 48 intervals, got 47",
  "schema_version": "v1"
}
```

## 3. Execution report (device to cloud)

Success:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "device_id": "allye-max300-001",
  "start_time": "2025-12-25T00:00:00Z",
  "end_time": "2025-12-25T00:30:00Z",
  "reported_at": "2025-12-25T00:30:01Z",
  "result": "EXECUTED",
  "schema_version": "v1"
}
```

Failure:

```json
{
  "message_id": "550e8400-e29b-41d4-a716-446655440000",
  "device_id": "allye-max300-001",
  "start_time": "2025-12-25T00:00:00Z",
  "end_time": "2025-12-25T00:30:00Z",
  "reported_at": "2025-12-25T00:05:00Z",
  "result": "FAILED",
  "error_code": "BMS_FAULT",
  "schema_version": "v1"
}
```

## 4. Heartbeat (device to cloud)

```json
{
  "device_id": "allye-max300-001",
  "sent_at": "2025-12-25T00:15:00Z",
  "current_interval_start": "2025-12-25T00:00:00Z",
  "schema_version": "v1"
}
```

## MQTT topic convention (example)

- Commands to device: `devices/{device_id}/commands`
- Acks from device: `devices/{device_id}/acks`
- Execution reports: `devices/{device_id}/reports`
- Heartbeats: `devices/{device_id}/heartbeat`
