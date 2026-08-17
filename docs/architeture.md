# Lab Architecture

## Environment

The laboratory consists of the following components:
```text
| Componentes                     |função                       |
|---|---|---||---|---|---||---|---|---||---|---|---||---|---|---|
| Kali Linux                      | Attack source               |
| Windows 10                      | Target endpoint             |
| Microsoft Defender for Endpoint | Endpoint telemetry          |
| Microsoft Defender XDR          | Detection and investigation |
| Microsoft Intune                | Endpoint management         |

```

--- 
## Network

The laboratory uses an isolated Host-Only network for
communication between the virtual machines.

### Attack Flow

```text
Kali Linux
↓
SMB / TCP 445
↓
Windows 10
↓
Windows authentication telemetry
↓
Microsoft Defender for Endpoint
↓
Microsoft Defender XDR
↓
Custom Detection
↓
Incident
↓
SOC Investigation

```
---

