---
title: "Money acceptors"
---

## Commands

### start
Instructs the device to start accepting bills or coin.

{{< hint type=warning >}}
If the device uses pulse interface and its control over cctalk, MDB, SSP isn't possible, then it's not possible to truly start or stop it. In this case the `moneyin` events aren't sent unless a `start` command is received.
{{< /hint >}}

```json
{"cmd": "start"}
```

### stop
Instructs the device to stop accepting money. In case of pulse interface devices, the `moneyin` events are withold.

```json
{"cmd": "stop"}
```

## Events 

### moneyin
Reports money input. It's up to the backend implementations to evaluate the currency and denominations.
```json
{
    "event": "moneyin"
    "data": {
        "amount": 100,
        "currency": "eur"
    }
}
```
