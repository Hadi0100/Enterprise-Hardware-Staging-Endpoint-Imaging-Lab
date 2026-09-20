# Enterprise-Hardware-Staging-Endpoint-Imaging-Lab

# Enterprise Hardware Staging & Endpoint Imaging Lab

## Project Goal

I'm building a small enterprise-style hardware staging and endpoint imaging lab using a Cisco Catalyst 2960X and a Dell OptiPlex 7060.

The goal of this project is to get hands-on experience with Cisco switching, networking, Windows deployment, endpoint configuration, and troubleshooting.

I don't want this to just be a documentation project. I'm using the equipment to actually practice the concepts and troubleshoot problems myself.

---

# Phase 1 — Cisco Switch Setup

## Hardware

* Cisco Catalyst 2960X-48LPS-L
* Dell OptiPlex 7060
* Ethernet cables
* Console cable
* Windows PC with PuTTY

## Console Access

I connected to the Cisco switch through the console port using PuTTY.

The switch was running:

* Cisco IOS 15.2(7)E9
* C2960X-UNIVERSALK9-M
* Catalyst 2960X-48LPS-L

I used the Cisco console connection to access the IOS CLI and complete the initial configuration.

## Basic Configuration

I configured the switch hostname as:

```text
STAGING-SW1
```

I also configured privileged access authentication and a management interface.

The switch management interface is currently using VLAN 1.

For my actual home lab, the switch was assigned a management IP on my local network.

I am intentionally not documenting my actual home-network IP addresses or passwords in this repository.

---

# What I Learned

## Cisco IOS Modes

I learned that Cisco IOS uses different command modes depending on what I'm doing.

```text
STAGING-SW1>
```

User EXEC mode.

```text
STAGING-SW1#
```

Privileged EXEC mode.

Configuration mode can be entered from privileged EXEC mode when changes need to be made.

One thing I learned from doing the setup myself is that the prompt is useful because it tells me what level of access I currently have.

---

## Management IP vs Physical Switch Ports

The switch has physical Ethernet interfaces such as:

```text
Gi1/0/1
Gi1/0/2
Gi1/0/3
...
Gi1/0/48
```

These are the ports where endpoints can physically connect.

The switch also has a logical management interface:

```text
Vlan1
```

The management interface has an IP address so the switch can communicate at Layer 3 for management purposes.

This helped me understand that the switch's physical Ethernet ports and its management interface are not the same thing.

---

# Commands I Used

### Check switch information

```text
show version
```

This showed me the switch model, IOS version, uptime, hardware information, and interfaces.

### View the active configuration

```text
show running-config
```

This showed the configuration currently running on the switch.

### Check interface status

```text
show interfaces status
```

This showed the physical switch ports, whether they were connected, their VLAN, speed, duplex, and interface type.

---

# Troubleshooting Lessons

One of the biggest things I want to take away from this project is learning how to troubleshoot systematically.

Instead of immediately changing configurations, I want to work from the physical layer upward.

My basic troubleshooting process is:

1. Check the physical connection.
2. Check link lights and interface status.
3. Check the switch port.
4. Check the VLAN.
5. Check MAC address learning.
6. Check the endpoint's IP configuration.
7. Check the subnet mask and gateway.
8. Test connectivity with ping.
9. Move to DNS, firewall, services, or applications if the basic network is working.

This gives me a structured way to troubleshoot instead of guessing.

---

# Important Discovery

When I checked the interface status, I initially noticed:

```text
Fa0    disabled    routed
```

I learned that this is not the normal endpoint port I should use on this switch.

The actual Ethernet ports on this Catalyst 2960X are:

```text
Gi1/0/1 - Gi1/0/48
```

This was a good lesson in checking the actual hardware and interface inventory instead of assuming the port names from another Cisco model.

---

# Current Lab Status

The Cisco switch is configured and the basic management setup is complete.

The Dell OptiPlex 7060 has not yet been connected to the switch for the endpoint portion of the lab.

## Next Steps

* Connect Dell OptiPlex 7060 to `Gi1/0/1`
* Verify physical link
* Learn MAC address table behavior
* Configure endpoint staging VLAN
* Configure access ports
* Test connectivity
* Learn trunking
* Learn basic STP/Rapid PVST
* Configure management VLAN
* Practice SSH management
* Build Windows 11 reference endpoint
* Practice endpoint imaging/deployment
* Apply applications and security configuration
* Perform endpoint validation
* Document troubleshooting scenarios

---

# Project Objective

The final goal is to simulate an enterprise endpoint staging workflow where multiple Windows endpoints can be connected to a controlled staging network, configured, imaged, tested, and prepared for deployment.

I'm using the lab to build practical skills that apply to NOC, data center, desktop support, endpoint support, and network support roles.
