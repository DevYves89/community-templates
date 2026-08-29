# Cisco Unified Communications Manager 15 by API

## Overview

Monitors Cisco Unified Communications Manager 15 through read-only AXL,
RISPort70, PerfMon, ControlCenter SOAP, and Cisco VOS Certificate Management
APIs. The template is exported for Zabbix 7.0 and stores its template release
as `vendor.name: Zabbix` and `vendor.version: 7.0-1`.

Maintainer: DevYves89

## Requirements

- Zabbix Server or Proxy 7.0 with network access to the CUCM HTTPS endpoints.
- A dedicated CUCM application user assigned to **Standard CCM Server
  Monitoring** and to a custom AXL read-only access control group containing
  **Standard AXL API Users** plus **Standard AXL Read Only API Access**.
- A separate Cisco VOS Certificate Management account created with privilege
  `0` (read APIs only) when certificate discovery is enabled.
- TLS trust configured at the Zabbix Server or Proxy for production systems.

## Configuration

Link the template to one CUCM node and set these host macros before collection:

| Macro | Required | Purpose |
|---|---:|---|
| `{$CUCM.URL}` | yes | CUCM API base URL, including port 8443. |
| `{$CUCM.AXL.URL}` | yes | CUCM Publisher AXL base URL. Use the Publisher value on every node host, including Subscribers. |
| `{$CUCM.FQDN}` | yes | Node FQDN for the independent HTTPS reachability check. |
| `{$CUCM.API.USER}` / `{$CUCM.API.PASSWORD}` | yes | Shared read-only AXL, PerfMon, RISPort70, and ControlCenter account and secret password. |
| `{$CUCM.NODE}` | yes | Exact CUCM node name used in PerfMon counter paths. |
| `{$CUCM.OS.USER}` / `{$CUCM.OS.PASSWORD}` | certificate only | Separate privilege-0 VOS Certificate Management account and secret password. |
| `{$CUCM.HTTP.PROXY}` | no | HTTP proxy URL for HTTP-agent collectors; empty means direct connection. |

Threshold, discovery-filter, expected-service, and polling macros are
documented in the template itself. Keep `{$CUCM.DB.REPLICATION.EXPECTED}` at
`0` on standalone Publishers; set it to `1` only when replication is known to
be expected. Service outage triggers are opt-in through the contextual
`{$CUCM.SERVICE.EXPECTED:"<service name>"}` macro.
SIP-trunk outage triggers use the same opt-in contract through
`{$CUCM.SIP.TRUNK.EXPECTED:"<trunk name>"}`.

Cisco documents the required access in the [AXL authentication
guide](https://developer.cisco.com/docs/axl/authentication/), [PerfMon getting
started guide](https://developer.cisco.com/site/sxml/learn/getting-started/perfmon/),
[RISPort getting started guide](https://developer.cisco.com/site/sxml/learn/getting-started/risport/),
and [Certificate Management authentication
guide](https://developer.cisco.com/docs/certificate-management/authentication/).

## Metrics and alerts

- API health for AXL, RISPort70, PerfMon, ControlCenter, and Certificate
  Management.
- CPU, memory, swap, uptime, process/thread counts, disk IOPS, partition and
  network-interface performance.
- Active and Inactive VOS image-partition utilization as informational values;
  their expected image occupancy is excluded from generic capacity alerts.
- CallManager media capacity, Average Expected Delay, Code Yellow/Red, and
  call-processing queue/rejection metrics.
- ControlCenter service state, reason, uptime and expected-service alarms.
- Certificate discovery with expiry, Subject, Issuer, and SAN metadata.
- SIP-trunk PerfMon call statistics plus bounded RISPort70 registration and
  registering-node discovery. A valid absent runtime record remains absent and
  is never fabricated as Down.
- An RTMT-inspired operations overview with common health/system/workload
  summary cards, performance graphs, AXL throttling, and current problems.

Raw transport/master items are tagged `scope=internal`; operator-facing items
are tagged `scope=operator`. Configure the discovery exclusion macros for the
local topology before enabling alerts for optional resources.

AXL is configuration inventory rather than a per-node runtime API, so
`{$CUCM.AXL.URL}` must name the Publisher for all hosts in a cluster. PerfMon,
RISPort70, and ControlCenter continue to use the node-local `{$CUCM.URL}`.

## Security and limitations

The template performs read-only API calls. Store passwords only as Zabbix
secret macros. CUCM API availability, installed services, PerfMon counters,
and certificate endpoints vary by deployment; unsupported APIs should be
disabled or correctly provisioned rather than treated as a zero metric.

## License

MIT License. By contributing this template, the maintainer agrees that this
file and associated template export are distributed under the MIT License.
