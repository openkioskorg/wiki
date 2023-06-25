---
title: Money acceptors (pulse)
---

[pulseacceptor](https://gitlab.com/openkiosk/pulseacceptor) repository contains a library and a daemon for coin acceptors and bill acceptors that use the pulse interface.

`pulseacceptor.go` contains a small library for counting pulses from a GPIO pin. The input is denoised and debounced.\
`cmd/pulse-watcher` contains a program for checking the pulse and pause widths sent by the device.\
`cmd/pulseacceptord` is a daemon that counts pulses, translates them into monetary values and sends this information to Redis.

## Debugging and finding config parameters using `pulse-watcher`
`pulse-watcher` is an useful program that prints widths of each pulse and pause. Upon exiting it also prints averages for both. You can use it to figure out values for debounce and denoise parameters for `pulseacceptord`.

Install by running:
```sh
go install gitlab.com/openkiosk/pulseacceptor/cmd/pulse-watcher@latest
```

Example usage:
```sh
# Listen on GPIO pin 17 for pulses, if no pulses follow after 101 milliseconds
# ignore this pause because pulses for this bill/coin has ended.
pulse-watcher -pin 17 -timeout 101ms
```

## Handling coins/cash with `pulseacceptord`
`pulseacceptord` denoises and debounces the pulses to prevent noise. After it is done counting pulses for a single bill/coin, it sends the event in to MQTT for queueing.


Install by running:
```sh
go install gitlab.com/openkiosk/pulseacceptor/cmd/pulseacceptord@latest
```

Change the values inside [`config.yaml`](https://gitlab.com/openkiosk/pulseacceptor/-/blob/master/cmd/pulseacceptord/config.yaml) before running.

**denoise**: Should be set to the pulse width. Although you might want to add or deduct a very little amount from this value to allow for some error. Pulse widths smaller than this value will be ignored.

**debounce**: Should be set to the pause width. Again, tune this value for possible errors like you should do for the `denoise` parameter. After a valid pulse (with width longer than the `denoise` parameter) has been received, more pulses will be ignored for the `debounce` amount. Meaning repeated pulses within `debounce` duration are ignored.

### Configuration options
`config.yaml`:
```yaml
device:
  pulse_pin: 17
  # Some devices write an extra low/high when beginning to send pulses
  # You might need this mode if you notice that always 1 pulse is missing
  plus_one_mode: true
  debounce: "110ms"
  denoise: "29ms"

# Some devices have "enable" pins to start/stop money input.
enable_pin_control: true
# Specify if enable_pin_control is enabled.
enable_pin: 27
# Is it enabled when the signal is high?
enabled_when_high: false

# pulses: amount
values:
  2: 10
  4: 20
  10: 50
  20: 100
  40: 200

mqtt:
  brokers:
    - "mqtt://127.0.0.1:1883"
  topic: "pulseacceptord"
  client_id: "pulseacceptord-1"
```

`pin` is the GPIO input pin that is connected to the COIN pin. The `values` map is for translating pulses to monetary values. In the above configuration file if your device were to send 2 pulses, `coinacceptord` would send an event with `"value": 10` to the backend.
