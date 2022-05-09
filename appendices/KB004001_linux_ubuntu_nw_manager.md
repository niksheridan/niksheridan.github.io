---
layout: page
title: KB004001 Using Ubuntu and Network Manager
---

A reference link to original webpage can be found [here](https://linuxhint.com/expertly-use-the-ubuntu-network-manager/),

## Show the network interfaces

List

```bash
nmcli connection show --active
```

Show details of the connection

```bash
nmcli connection show 'Wired connection 1'
```

Modify the connection

```bash
sudo nmcli connection modify <connection-id> <parameter> <value>
```

Restart interface

```bash
nmcli connection down <interface-name>
nmcli connection up <interface-name>
```
