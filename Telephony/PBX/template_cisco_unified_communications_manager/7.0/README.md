# Cisco Unified Communications Manager 15 by API

## Overview

Monitors Cisco Unified Communications Manager 15 through read-only AXL,
RISPort70, PerfMon, ControlCenter SOAP, and Cisco VOS Certificate Management
APIs. The template is exported for Zabbix 7.0 and has template release version
`1.3.0` in `{$CUCM.TEMPLATE.VERSION}`.

Maintainer: DevYves89

## Requirements

- Zabbix Server or Proxy 7.0 with network access to the CUCM HTTPS endpoints.
- A least-privilege CUCM API account named `zbx_monitor` (or an equivalent
  account) with only the read permissions required by the enabled APIs.
- A separate least-privilege Cisco VOS account for Certificate Management when
  certificate discovery is enabled.
- TLS trust configured at the Zabbix Server or Proxy for production systems.

## Configuration

Link the template to one CUCM node and set these host macros before collection:

| Macro | Required | Purpose |
|---|---:|---|
| `{$CUCM.URL}` | yes | CUCM API base URL, including port 8443. |
| `{$CUCM.API.HOST}` / `{$CUCM.API.USER}` | yes | CUCM PerfMon session endpoint hostname and API username. |
| `{$CUCM.FQDN}` | yes | Node FQDN for the independent HTTPS reachability check. |
| `{$CUCM.USER}` / `{$CUCM.PASSWORD}` | yes | Read-only CUCM API account and secret password. |
| `{$CUCM.NODE}` | yes | Exact CUCM node name used in PerfMon counter paths. |
| `{$CUCM.OS.USER}` / `{$CUCM.OS.PASSWORD}` | certificate only | Read-only VOS Certificate Management account and secret password. |
| `{$CUCM.HTTP.PROXY}` | no | HTTP proxy URL for HTTP-agent collectors; empty means direct connection. |

Threshold, discovery-filter, expected-service, and polling macros are
documented in the template itself. Keep `{$CUCM.DB.REPLICATION.EXPECTED}` at
`0` on standalone Publishers; set it to `1` only when replication is known to
be expected. Service outage triggers are opt-in through the contextual
`{$CUCM.SERVICE.EXPECTED:"<service name>"}` macro.

## Metrics and alerts

- API health for AXL, RISPort70, PerfMon, ControlCenter, and Certificate
  Management.
- CPU, memory, swap, uptime, process/thread counts, disk IOPS, partition and
  network-interface performance.
- CallManager media capacity and call-processing queue/rejection metrics.
- ControlCenter service state, reason, uptime and expected-service alarms.
- Certificate discovery with expiry, Subject, Issuer, and SAN metadata.
- SIP-trunk PerfMon call statistics; runtime registration is deliberately not
  inferred when RIS has no runtime record.
- A full-HD overview dashboard for CPU, memory, and swap utilization.

Raw transport/master items are tagged `scope=internal`; operator-facing items
are tagged `scope=operator`. Configure the discovery exclusion macros for the
local topology before enabling alerts for optional resources.

## Security and limitations

The template performs read-only API calls. Store passwords only as Zabbix
secret macros. CUCM API availability, installed services, PerfMon counters,
and certificate endpoints vary by deployment; unsupported APIs should be
disabled or correctly provisioned rather than treated as a zero metric.

## License

MIT License. By contributing this template, the maintainer agrees that this
file and associated template export are distributed under the MIT License.
