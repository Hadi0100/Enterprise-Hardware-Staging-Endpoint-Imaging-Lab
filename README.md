# Enterprise Hardware Staging & Endpoint Imaging Lab

# Day 1 — Cisco Switching & Endpoint Connectivity

Today I started building the networking side of my Enterprise Hardware Staging & Endpoint Imaging Lab.

The goal is to make this more than just a project I can put on my resume. I want to actually understand what is happening when a workstation connects to a switch, how VLANs work, how the switch learns devices, and how to troubleshoot connectivity problems.

## Hardware

* Cisco Catalyst 2960X-48LPS-L
* Dell OptiPlex 7060
* Windows 11 workstation
* Ethernet cable
* PuTTY for Cisco console access

## Cisco Switch Setup

I connected to the Cisco switch through the console using PuTTY and completed the initial setup.

Configured:

* Switch hostname
* Enable authentication
* Management interface
* Management IP
* Subnet configuration
* Basic switch management settings

The switch is currently using:

```text
Hostname: STAGING-SW1
Management IP: 192.168.12.2
Subnet Mask: 255.255.255.0
Management VLAN: VLAN 1
```

I am not putting passwords or other sensitive information in this repository.

## First Endpoint Connection

I connected the Dell OptiPlex 7060 to:

```text
Gi1/0/1
```

I checked the port with:

```text
show interfaces status
```

The port came up as:

```text
connected
a-full
a-1000
10/100/1000BaseTX
```

This confirmed that the physical Ethernet connection was working and the workstation negotiated a 1 Gbps full-duplex connection.

## MAC Address Learning

I then checked the switch's MAC address table:

```text
show mac address-table
```

The switch learned the OptiPlex's Ethernet MAC address:

```text
6c02.e048.4eee
```

on:

```text
Gi1/0/1
```

The entry was dynamic.

This was one of the first things I wanted to actually see instead of just reading about it. The switch is learning the source MAC address from Ethernet traffic and associating it with the physical port where it was received.

## Interface Troubleshooting

I also checked:

```text
show interfaces gi1/0/1
```

The interface showed:

* Full duplex
* 1000 Mb/s
* 0 input errors
* 0 CRC errors
* 0 collisions
* 0 output errors
* No output drops

This gave me practice reading interface counters instead of just looking at whether a port says "connected."

I learned that interface counters and the 5-minute traffic rate are different things. The interface can have packets received historically while the current 5-minute rate can still be 0 if there is little or no traffic happening at that moment.

## First Connectivity Problem

I initially tried to ping the Cisco management IP from the OptiPlex and got:

```text
Destination host unreachable
```

At first it looked like the Cisco configuration might be wrong.

I started troubleshooting from the bottom up instead of immediately changing configuration.

I checked:

1. Physical connection
2. Switch port status
3. MAC address learning
4. VLAN assignment
5. Switch management interface
6. Windows IP configuration
7. ARP

## What I Found

The OptiPlex actually had multiple network adapters.

The Wi-Fi adapter had:

```text
192.168.12.163/24
```

but the Ethernet adapter connected to the Cisco had:

```text
169.254.164.253/16
```

The Ethernet adapter also had no default gateway.

The Cisco had already learned the Ethernet adapter's MAC address, so the physical connection was working. The problem was that the wired Windows interface did not have an address on the same network as the Cisco management interface.

This was a good lesson for me because I initially looked at the computer's IP address and assumed it belonged to the Ethernet connection. It actually belonged to Wi-Fi.

## Testing With a Static IP

For testing, I temporarily configured the wired Ethernet adapter with:

```text
IP Address: 192.168.12.164
Subnet Mask: 255.255.255.0
Default Gateway: blank
```

I temporarily disabled Wi-Fi so I could test the wired connection by itself.

Then I tested:

```text
ping 192.168.12.2
```

The result was:

```text
Reply from 192.168.12.2
Reply from 192.168.12.2
Reply from 192.168.12.2
```

The first packet timed out, but the following packets succeeded.

## ARP

After the successful ping, I checked:

```text
arp -a
```

The Ethernet interface showed:

```text
192.168.12.2
30-8b-b2-29-78-40
dynamic
```

This showed that the OptiPlex successfully resolved the Cisco's IP address to its MAC address using ARP.

This helped me understand the relationship between:

```text
IP address → MAC address → switch port
```

The switch also learned the OptiPlex's MAC address:

```text
6c02.e048.4eee → Gi1/0/1
```

So I was able to see both sides of the process instead of just memorizing what ARP and MAC tables are.

## What I Learned Today

The biggest thing I learned today was to troubleshoot in layers instead of randomly changing settings.

My current troubleshooting process is:

```text
Physical link
     ↓
Interface status
     ↓
MAC address learning
     ↓
VLAN
     ↓
IP configuration
     ↓
ARP
     ↓
Ping / connectivity
```

I also learned:

* A switch can have a physical Ethernet interface and a separate logical management interface.
* A switch learns MAC addresses dynamically from incoming Ethernet frames.
* VLAN 1 is currently the default VLAN on my switch ports.
* A `169.254.x.x` address on Windows can indicate that the Ethernet adapter did not receive a DHCP address.
* A computer can have multiple network adapters with completely different IP configurations.
* `arp -a` can be useful when troubleshooting local IPv4 connectivity.
* `show interfaces` gives much more information than simply checking whether a port is connected.
* The first packet of a ping can sometimes be lost while address resolution is taking place.

## Current Status

Cisco switch:

```text
STAGING-SW1
192.168.12.2/24
VLAN 1
```

Dell OptiPlex wired Ethernet:

```text
192.168.12.164/24
```

Connectivity between the wired OptiPlex and Cisco management interface:

```text
WORKING
```

## Next Steps

Tomorrow I want to continue with:

* Understanding VLANs
* Creating a dedicated staging VLAN
* Configuring switch access ports
* Understanding trunk ports
* Learning more about MAC address tables
* Setting up DHCP for the lab
* Testing DHCP vs static addressing
* Basic switch security
* SSH management
* Windows endpoint staging
* Eventually building the imaging/deployment portion of the project

The main goal is to keep learning by actually doing the configuration and troubleshooting instead of just following commands.
