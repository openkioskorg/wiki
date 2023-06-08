---
title: Getting started
weight: -20
---

In OpenKiosk architecture each component is controlled by its own daemon. If data loss prevention is important, these daemons push messages to event queues (MQTT broker).

Components (MQTT publishers) talk to a backend program (MQTT subscriber) using the OpenKiosk protocol. The backend is responsible for listening to events, evaluating them and taking action. The result is communicated to the frontend over websockets.

<img src="/openkiosk-arch-graph.svg" width="50%" style="margin: 3rem auto; display: block;">
