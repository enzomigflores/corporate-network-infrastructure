# Corporate Network Infrastructure

A sanitized portfolio case study of a managed office network designed around centralized administration, wireless coverage, VLAN segmentation, controlled resource access, managed endpoints, monitoring, and operational maintainability.

The production environment was implemented using **TP-Link Omada SDN** with an **ER707-M2 gateway**, **OC200 hardware controller**, and **5 × EAP610 Wi-Fi 6 access points**.

> This repository documents the architecture and engineering decisions behind a real production environment. Organization-specific identities, credentials, SSIDs, IP addresses, MAC addresses, management URLs, and other sensitive configuration details have been removed or replaced with illustrative examples.

## What this project demonstrates

- Centralized network management through Omada SDN
- Multi-access-point Wi-Fi deployment with roaming support
- VLAN-based segmentation for restricted resources
- Inter-VLAN access control using ordered gateway ACLs
- Managed endpoint identification and DHCP reservations
- Dedicated connectivity models for general and restricted network resources
- Per-client traffic controls and application visibility
- Operational monitoring, maintenance, and change-control practices

## Environment

| Component | Deployment |
|---|---|
| Gateway | TP-Link ER707-M2 |
| Controller | TP-Link OC200 |
| Wireless | 5 × TP-Link EAP610 Wi-Fi 6 APs |
| AP revisions | 3 × V3, 2 × V4 |
| Management | Omada SDN |
| Restricted resource example | Brother DCP-T830DW |
| Endpoint addressing | DHCP with reservations where stable identity is required |

## High-level topology

```text
Internet / Primary ISP
          │
          ▼
    ER707-M2 Gateway
       ┌──┴───────────────┐
       │                  │
       ▼                  ▼
  PoE Switching       OC200 Controller
       │
       ▼
  5 × EAP610 APs
       │
       ├──────────────► Corporate Wireless Clients
       ├──────────────► General Network Resources
       └──────────────► Restricted Resource VLAN
```

## Logical design

The office network separates normal employee connectivity from resources that require tighter control.

```text
Managed Employee Endpoint
          │
          ▼
   Corporate Network
      │         │
      │         └──────────────► Internet Access
      │
      ▼
   Gateway ACL
    │       │
    │       └── Not Authorized ──► Access Denied
    │
    └── Authorized ──────────────► Restricted Resource VLAN
```

A general printer network can remain on the standard office LAN when no dedicated restriction is required. Resources that require selective access are placed in a dedicated VLAN and reached only through explicit gateway policy.

## Restricted-resource access model

The restricted-resource design uses two logical groups:

- **Authorized Endpoints** — managed devices with stable DHCP reservations
- **Restricted Resource** — the protected endpoint represented as a single host

ACL evaluation follows an allow-before-deny model:

```text
Authorized Endpoints
        │
        │  PERMIT
        ▼
Restricted Resource
        ▲
        │  DENY
        │
All Other Endpoints
```

The specific implementation is documented in [Access Control](docs/access-control.md).

## Managed endpoints

Devices that participate in access-control policy are first identified in the controller using their active network-adapter MAC address. Where a stable address is required, the endpoint receives a **DHCP reservation from the network infrastructure** rather than a manually configured static address in the operating system.

This provides a stable identity for policy membership while allowing client devices to remain configured for automatic DHCP.

## Wireless design

The wireless environment uses five EAP610 access points managed through Omada.

Key design choices include:

- Wired AP backhaul as the preferred operating state
- 2.4 GHz and 5 GHz coverage for general users
- 5 GHz preference where practical
- Fast roaming support
- Roaming-assistance features to reduce sticky-client behavior
- Per-client traffic shaping
- Centralized application and client visibility
- Separate wireless connectivity for resources that require VLAN placement

## Monitoring and operations

The deployment is operated as an infrastructure system rather than a one-time installation. Ongoing practices include:

- Reviewing client distribution and application activity
- Maintaining DHCP reservations for managed devices
- Reviewing access-group membership when devices or responsibilities change
- Periodic firmware maintenance
- Controller configuration backups
- Change documentation for VLAN, ACL, wireless, DHCP, and security-policy changes
- Coverage and roaming validation after infrastructure changes

See [Operations](docs/operations.md) for the public runbook summary.

## Design decisions

### Why centralized management?

The OC200 provides a persistent management plane for the gateway and access points without depending on an administrator workstation remaining online.

### Why DHCP reservations instead of manual static IPs?

Reservations keep endpoint configuration centralized and reduce workstation-side configuration drift while still providing stable addresses for policy use.

### Why a dedicated VLAN for restricted resources?

A separate VLAN creates a clear security boundary. Access can then be explicitly granted to approved endpoints without forcing those employees to switch away from their normal corporate network.

### Why ordered ACLs?

The policy needs to allow a known set of devices first, then deny everyone else from the protected resource. Rule order is therefore part of the security design, not merely an implementation detail.

## Repository structure

```text
office-network-infrastructure/
├── README.md
└── docs/
    ├── network-design.md
    ├── access-control.md
    └── operations.md
```

## Future improvements

Potential next steps for the environment include:

- Secondary WAN connectivity with automatic failover
- 802.1X / RADIUS-based endpoint identity
- Centralized DNS logging
- Long-term syslog retention
- Expanded managed-device inventory
- Additional VLAN segmentation as new restricted services are introduced
- Further wireless feature tuning after compatibility validation

## Public portfolio edition

This repository intentionally does **not** contain production configuration exports, passwords, controller addresses, live SSIDs, internal IP plans, MAC addresses, employee identities, or administrative credentials.

Its purpose is to demonstrate the network architecture, access-control model, implementation reasoning, and operating practices without exposing the environment itself.
