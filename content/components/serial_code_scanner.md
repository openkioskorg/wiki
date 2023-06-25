---
title: QR/Barcode scanners (serial)
---

[codescanner](https://gitlab.com/openkiosk/codescanner) repository contains a library and an OpenKiosk protocol daemon.

You will need root privileges for device read/write unless your user is added to the dialout group.

## Scanning with `codescannerd`

Install by running:
```sh
go install gitlab.com/openkiosk/codescanner/cmd/codescannerd@latest
```

### Configuration options:

`config.yaml`:
```yaml
device:
  portname: "/dev/ttyACM0"
  # Read this many bytes at a time
  bufflen: 150
  # After a successfull read pause this much
  debounce: "1s"

mqtt:
  brokers:
    - "mqtt://127.0.0.1:1883"
  topic: "codescannerd"
  client_id: "codescannerd-1"
```

## My serial device keeps changing it's mapped name, what do I do?
Add a udev rule to ensure the device has a persistent name.

Extract information about the device first:
```sh
# Replace the X's with the name of your device
udevadm info --name=/dev/ttyXXXX --attribute-walk
```

Write the rules file `/etc/udev/rules.d/99-usb-serial.rules`:
```
SUBSYSTEM=="tty", ATTRS{idVendor}=="0000", ATTRS{serial}=="AAAAAA", SYMLINK+="ACM0"
```

Replace the vendor ID and serial with the values from the `udevadm info` command above. Set symlink name to the name set in the `config.yaml`.

Either restart your computer or run this to apply changes:
```sh
udevadm control --reload-rules && udevadm trigger
```
