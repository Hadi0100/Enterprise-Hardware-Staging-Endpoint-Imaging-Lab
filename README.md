# Enterprise Hardware Staging & Endpoint Imaging Lab

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
Management IP: Private lab address
Subnet Mask: /24
Management VLAN: VLAN 1
```

I am not putting passwords, actual IP addresses, MAC addresses, or other sensitive information in this repository.

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

The switch learned the OptiPlex's Ethernet MAC address on:

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

I initially tried to ping the Cisco management interface from the OptiPlex and got:

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

The Wi-Fi adapter had an address on the home network, but the Ethernet adapter connected to the Cisco had a `169.254.x.x` link-local address.

The Ethernet adapter also had no default gateway.

The Cisco had already learned the Ethernet adapter's MAC address, so the physical connection was working. The problem was that the wired Windows interface did not have an address on the same network as the Cisco management interface.

This was a good lesson for me because I initially looked at the computer's IP address and assumed it belonged to the Ethernet connection. It actually belonged to Wi-Fi.

## Testing With a Static IP

For testing, I temporarily configured the wired Ethernet adapter with a static address on the same private /24 lab network as the Cisco management interface.

The default gateway was left blank because I was only testing local connectivity between the workstation and switch.

I temporarily disabled Wi-Fi so I could test the wired connection by itself.

Then I tested connectivity to the Cisco management interface with:

```text
ping <switch-management-address>
```

The first packet timed out, but the following packets succeeded.

This confirmed that the wired OptiPlex could communicate with the Cisco switch management interface.

## ARP

After the successful ping, I checked:

```text
arp -a
```

The Ethernet interface showed a dynamic ARP entry for the Cisco management interface.

This showed that the OptiPlex successfully resolved the Cisco's IP address to its MAC address using ARP.

This helped me understand the relationship between:

```text
IP address → MAC address → switch port
```

The switch also learned the OptiPlex's MAC address on `Gi1/0/1`.

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
* ARP maps an IPv4 address to a MAC address on the local network.
* The first ping timed out while the following packets succeeded, which gave me a reason to investigate ARP and verify the Ethernet connection instead of assuming the configuration was broken.

## Current Status

Cisco switch:

```text
STAGING-SW1
Private /24 management network
VLAN 1
```

Dell OptiPlex wired Ethernet:

```text
Static address on the private /24 lab network
```

Connectivity between the wired OptiPlex and Cisco management interface:

```text
WORKING
```

The main goal is to keep learning by actually doing the configuration and troubleshooting instead of just following commands.
