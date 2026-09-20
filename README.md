# Enterprise-Hardware-Staging-Endpoint-Imaging-Lab

# Cisco Catalyst 2960 — Basic Switch Configuration

## What I was trying to do

I started building a small enterprise-style hardware staging lab using a Cisco Catalyst 2960 and a Dell OptiPlex 7060.

The goal is to use the lab to practice Cisco switching, endpoint staging, Windows deployment, and troubleshooting instead of just following tutorials.

## Equipment

* Cisco Catalyst 2960
* Dell OptiPlex 7060
* Ethernet cables
* Console cable
* Windows PC for switch configuration

## What I configured

I connected to the Cisco 2960 through the console port and went through the initial setup.

I configured:

* Switch hostname: `STAGING-SW`
* Enable secret for privileged access
* Management interface
* Management IP addressing
* Subnet mask
* Default gateway

I also learned how to move between the different Cisco IOS modes:

```text
STAGING-SW>       User EXEC
STAGING-SW#       Privileged EXEC
STAGING-SW(config)#   Global configuration
```

## Networking concepts I learned

One of the main things I wanted to understand was how the switch actually fits into the network.

My lab network is using a `/24` subnet, which means the subnet mask is:

```text
255.255.255.0
```

The switch needs a management IP so I can communicate with and manage it over the network.

I also learned the difference between a switch's physical Ethernet ports and its management interface. The management interface gives the switch an IP address for management, while the physical switch ports are used to connect devices to the network.

## Troubleshooting approach

I'm trying to build the habit of troubleshooting from the bottom up instead of randomly changing settings.

My basic process is:

1. Check the physical connection and link lights.
2. Check whether the switch port is up.
3. Check the VLAN assigned to the port.
4. Check whether the switch is learning the device's MAC address.
5. Check the device's IP address and subnet mask.
6. Check the default gateway.
7. Test connectivity with ping.
8. If the network works, move up to DNS, firewall, and application-level troubleshooting.


The goal is to eventually turn this into a small-scale simulation of an enterprise endpoint staging environment.


