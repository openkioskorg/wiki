## Transport
OpenKiosk architecture uses JSON-over-MQTT for backend-component communication, and JSON-over-Websocket for backend-frontend communication.

{{< hint type=important >}}
Everything described in this section is subjected to change in the future as the project evolves.
{{< /hint >}}

## General Packet Structure

### Type: Command
Commands are instructions sent from the backend to the microservice components. For example: a "start" command is sent to the bill acceptor in order to get it ready to start accepting bills.

```json
{
    "cmd": "start",
    "data": "this field exists but is ignored in this case!"
}
```

### Type: Event
Events are caused by user interactions or they're results of commands issued by the backend. For example: after a bill acceptor has been started, it will send events for the amount of bills inputted.

```json
{
    "event": "moneyin",
    "data": {
        "amount": 100,
        "currency": "eur"
    }
}
```
