# CME + Voice VLAN Lab

Two-site topology (HQ + Branch) with IP phones, VLAN-separated voice/data
traffic, and Cisco CME (Call Manager Express) providing call control at
each site.

## Topology
![Topology](configs/topology.png)

- **HQ**: VLAN 10 (Data, 10.0.10.0/24), VLAN 20 (Voice, 10.0.20.0/24)
- **Branch**: VLAN 30 (Data, 10.0.30.0/24), VLAN 40 (Voice, 10.0.40.0/24)
- HQ-CME (10.0.20.2) and BR-CME (10.0.40.2) each handle call registration
  for their site's IP phones

## What this covers
- Voice VLAN vs Data VLAN separation on access switch ports
  (`switchport voice vlan`)
- DHCP with separate scopes for voice/data, option 150 pointing phones to
  their local CME for TFTP
- Cisco Unified CME - `telephony-service`, `ephone-dn`, `ephone` config
- Router-on-a-stick (dot1Q subinterfaces) for inter-VLAN routing

## Key issues hit and resolved
- DHCP ping-conflict caused by gateway addresses not being excluded from
  the DHCP pool
- CME router (2811, no switch module) doesn't support `switchport` —
  had to assign the IP directly to a routed physical interface instead of
  using an SVI
- Switch port facing CME left in trunk mode, causing dropped frames
  ("no subinterface with this VLAN enabled") — fixed by setting it to
  access mode in the voice VLAN
- `ip source-address` in `telephony-service` had to exactly match CME's
  own interface IP for phones to register successfully

## Configs
See [`/configs`](./configs) for full running-configs of each device:
HQ-CME, HQ-SWT, HQ-RTR, BR-CME, BR-SWT, BR-RTR.
