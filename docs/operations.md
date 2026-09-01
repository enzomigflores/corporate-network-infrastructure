# Operations

This document summarizes the operational practices used to keep the network maintainable without exposing production-specific procedures or configuration values.

## Access-point deployment

A new access point is introduced through the controller, updated to the appropriate firmware track, assigned a clear location-based identity, and validated for both coverage and roaming.

The target operating state is wired backhaul.

If an AP unexpectedly reports wireless uplink, the first troubleshooting path is physical connectivity: verify the switch-port link, patch-panel mapping, cable continuity, and the same path with a known-good endpoint before treating the problem as a wireless issue.

## Roaming validation

Roaming should be tested after major AP or radio changes. A practical validation method is to maintain a continuous call or stream while moving between coverage areas, then confirm that the client transitions between access points without a disruptive disconnect.

## Managed endpoint registration

For endpoints that require policy-based access:

1. Identify the active Ethernet or Wi-Fi adapter.
2. Match the adapter MAC with the controller client record.
3. Assign a clear managed-device identity.
4. Enable a DHCP reservation when stable addressing is required.
5. Leave the workstation configured for automatic DHCP.
6. Add the reserved address only to the policy groups required for that endpoint.
7. Validate both permitted and denied traffic.

## Monitoring

Regular review focuses on client distribution, application activity, roaming behavior, DHCP reservations, stale managed devices, ACL group membership, and protected-resource access issues.

Monitoring is used primarily for troubleshooting, capacity awareness, and policy maintenance.

## Controller backups

Controller configuration backups should be exported on a regular schedule and stored off-device.

A simple retention model is weekly export, keeping the most recent several copies, with at least one copy outside the controller itself.

Production backup paths and controller addresses are intentionally omitted from this public edition.

## Firmware maintenance

Gateway and access-point firmware should be reviewed periodically. Because the deployment contains multiple hardware revisions of the same AP family, firmware must always be matched to the correct hardware revision.

Significant upgrades should be performed during an appropriate maintenance window.

## Change control

Changes affecting VLANs, ACLs, wireless networks, DHCP, controller settings, security policies, infrastructure addressing, and managed-device authorization should be documented.

For higher-risk changes, record the purpose, previous state, new state, expected impact, validation result, and rollback approach.

## Access reviews

Restricted-resource authorization should be reviewed whenever devices, employees, or operational responsibilities change.

Stale reservations and stale authorization entries should be removed rather than left indefinitely.

## Future operational improvements

The environment can be strengthened further through centralized long-term syslog retention, internal DNS logging, 802.1X / RADIUS, a broader asset inventory, secondary WAN service with automatic failover, and expanded segmentation for additional restricted services.
