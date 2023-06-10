---
title: QR/Barcode scanners
---

## Commands

### start

```json
{"cmd": "start"}
```

### stop

```json
{"cmd": "stop"}
```

## Events

### codescan

Happens on scan, contains base64 encoded bytes in data field.
```json
{
    "event": "codescan",
    "data": "SGVsbG8gOik="
}
```
