# Cisco Unity Connection 15 by API

## Overview

Monitors Cisco Unity Connection 15 through read-only VMREST/CUPI, Cisco
Serviceability PerfMon, ControlCenter SOAP, and Cisco VOS Certificate
Management APIs. The template is exported for Zabbix 7.0 and has template
release version `1.3.0` in `{$CUC.TEMPLATE.VERSION}`.

Maintainer: DevYves89

## Requirements

- Zabbix Server or Proxy 7.0 with network access to the Unity HTTPS endpoints.
- A least-privilege Unity monitoring account named `zbx_monitor` (or an
  equivalent account) with read-only VMREST/CUPI and Serviceability access.
- A separate least-privilege Cisco VOS account for Certificate Management when
  certificate discovery is enabled.
- TLS trust configured at the Zabbix Server or Proxy for production systems.

## Configuration

Link the template to one Unity Connection node and set these host macros:

| Macro | Required | Purpose |
|---|---:|---|
| `{$CUC.URL}` | yes | Unity base URL, without a trailing slash. |
| `{$CUC.SERVICEABILITY.URL}` | yes | Cisco Serviceability SOAP base URL, normally port 8443. |
| `{$CUC.CERT.URL}` | certificate only | Cisco VOS Certificate Management base URL. |
| `{$CUC.API.USER}` / `{$CUC.API.PASSWORD}` | yes | Read-only Unity account and secret password. |
| `{$CUC.OS.USER}` / `{$CUC.OS.PASSWORD}` | certificate only | Read-only VOS account and secret password. |
| `{$CUC.NODE}` | yes | Exact Unity node name used in PerfMon counter paths. |
| `{$CUC.HTTP.PROXY}` | no | HTTP proxy URL for HTTP-agent collectors; empty means direct connection. |

Configure the supplied interval, threshold, and discovery-exclusion macros to
match the local deployment. `{$CUC.TELEPHONY.REQUIRED}` controls whether the
Unity telephony baseline is considered mandatory for alerts.

## Metrics and alerts

- VMREST availability, version, and inventory counts.
- CPU, memory, swap, system, partition, and network-interface PerfMon data.
- Voice-port utilization, calls, MWI failures, voice sessions, failsafes, and
  opening-greeting delay.
- ControlCenter service discovery and state.
- Identity-certificate discovery with expiry, Subject, Issuer, and SAN
  metadata.
- A full-HD overview dashboard for CPU, memory, and swap utilization.

Raw transport/master items are tagged `scope=internal`; operator-facing items
are tagged `scope=operator`. The template collects only read-only endpoints
and does not start, stop, restart, or otherwise modify Unity services.

## Security and limitations

Store passwords only as Zabbix secret macros. Install the appropriate CA chain
and use certificate verification in production. Available PerfMon counters and
services vary by Unity Connection version and deployment.

## License

MIT License. By contributing this template, the maintainer agrees that this
file and associated template export are distributed under the MIT License.
