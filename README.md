# Loxone Smart Home Projects

> A collection of local, well-documented integrations for Loxone, Raspberry Pi / DietPi, and selected smart-home devices.

These projects grew out of real installations and share one goal: integrate devices and services into Loxone in a **local, transparent, and robust** way whenever technically possible, without adding unnecessary platforms or cloud dependencies.

Public documentation is maintained in English. Issues and pull requests should preferably be written in English so that findings remain useful to an international audience.

## Projects

| Project | Release | Purpose |
| --- | --- | --- |
| [loxone-mhi-bridge](https://github.com/therealb4n4na/loxone-mhi-bridge) | `v3.3.0` | Local integration of Mitsubishi Heavy Industries air conditioners using WF-RAC adapters, including passive polling, verified control commands, and multi-split conflict handling. |
| [loxone-bayrol-bridge](https://github.com/therealb4n4na/loxone-bayrol-bridge) | `v2.0.0` | BAYROL pool data for Loxone, local status caching, and controlled pH auto/off handling through MQTT/WebSocket. |
| [loxone-km200-bridge](https://github.com/therealb4n4na/loxone-km200-bridge) | `v1.0.0` | Local Buderus/Bosch KM200 access with selected verified writes and optional DHW/heating history. |
| [loxone-desk-lamp-bridge](https://github.com/therealb4n4na/loxone-desk-lamp-bridge) | `v1.0.0` | Small local miIO bridge for Xiaomi/Yeelight-compatible desk lamps using Loxone Lumitech values. |

## Shared design principles

### Local first

Where technically possible, integrations run entirely inside the local network. Cloud communication is used only when the target system itself requires it.

### Reading is not writing

A periodic poller should not silently change devices. Status collection and control commands are deliberately separated.

### Verify writes

A successfully transmitted HTTP or MQTT request does not automatically prove that a physical device accepted the requested state. Important write operations are therefore verified by reading the actual state back whenever possible.

### Keep Loxone simple

Protocol details, plausibility checks, retries, caching, and error handling belong inside the bridge. The Loxone side should need only a small set of clear HTTP inputs, outputs, and status codes.

### Make failures diagnosable

`systemd active` does not prove that the underlying device or gateway is reachable. Projects distinguish, where possible, between:

- process/service health
- device or gateway reachability
- data age and data quality
- successful and failed control operations

### No secrets in Git

Production passwords, tokens, device identities, state files, logs, captures, and installation-specific configuration do not belong in the repository. Example configuration files and `.gitignore` rules are used instead.

## Typical architecture

```text
Device / Gateway / Cloud
          │
          ▼
   Python bridge on Linux
   ├─ protocol / API logic
   ├─ state cache
   ├─ error handling
   ├─ verification
   └─ HTTP API
          │
          ▼
        Loxone
```

The bridges typically run as `systemd` services on DietPi/Debian. A Raspberry Pi is sufficient for many of these integrations.

## Versioning

The projects follow [Semantic Versioning](https://semver.org/):

```text
MAJOR.MINOR.PATCH
```

- **MAJOR** – incompatible API, configuration, or behavior changes
- **MINOR** – backward-compatible new functionality
- **PATCH** – fixes and small backward-compatible changes

Stable states receive a Git tag and a GitHub Release. Each project's `CHANGELOG.md` records notable changes.

## Installation

There is intentionally no universal installer for the complete collection because the devices, protocols, dependencies, and security requirements differ too much.

Start with the README of the project you want to use. A typical workflow is:

```text
1. Clone the repository
2. Copy the example configuration and adapt it locally
3. Install Python dependencies
4. Test the bridge locally
5. Configure the systemd service
6. Verify the HTTP endpoints
7. Configure Loxone only after the bridge is known to work
```

## Security

These projects are intended for trusted local home/building networks. Write-capable HTTP endpoints should not be exposed directly to the public Internet.

Where possible, write access is restricted to a configured controller IP in addition to network-level firewall rules. See each project's `SECURITY.md` for implementation-specific guidance.

## Contributions and technical findings

Bug reports, documented hardware variants, protocol observations, and pull requests are welcome. For vendor-specific or reverse-engineered interfaces, distinguish clearly between:

- **Verified** – reproduced on real hardware
- **Experimental** – plausible and tested, but not yet sufficiently confirmed
- **Unknown** – observed, but meaning is still unclear

The goal is to make findings reusable and reviewable instead of publishing unexplained values or "magic" IDs.

## Scope of this collection

This repository intentionally lists only **generally reusable public integrations**. Private or installation-specific projects are not included.

## License

Unless a linked repository states otherwise, the software projects in this collection use the MIT License. The `LICENSE` file in each individual repository is authoritative.
