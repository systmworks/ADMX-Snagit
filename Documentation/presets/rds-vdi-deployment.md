# RDS / VDI Deployment

Tuned for Remote Desktop Services, Citrix, or non-persistent VDI hosts: applies TechSmith's documented virtualization optimizations, removes the printer-capture destination, keeps the Snagit Library path fixed, and puts update timing under IT control.

Ready-to-import registry file: [`Presets/RdsVdiDeployment.reg`](../../Presets/RdsVdiDeployment.reg) - covers both Snagit 2025 and Snagit 2026 in one file; no Group Policy infrastructure required. For centrally managed deployment, set the matching policy for your Snagit version under **Computer Configuration \ Administrative Templates \ Snagit \ Snagit 2025 (or 2026)** (or **User Configuration** for user-scope policies) instead.

| Policy | Snagit Version | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|---|
| Set Virtual Machine Display Optimization | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!VMOverride` = `OtherVM` | OtherVM | TechSmith recommends always applying the virtualization display optimizations (OtherVM) when deploying with virtualization technologies, rather than relying on auto-detection. |
| Set Virtual Machine Display Optimization | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!VMOverride` = `OtherVM` | OtherVM | TechSmith recommends always applying the virtualization display optimizations (OtherVM) when deploying with virtualization technologies, rather than relying on auto-detection. |
| Disable Printer Capture | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!NoPrinterCaptureSupport` = 1 | Enabled | TechSmith's deployment guide specifically calls out disabling Printer Capture when deploying in a server environment such as Remote Desktop Services. |
| Disable Printer Capture | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!NoPrinterCaptureSupport` = 1 | Enabled | TechSmith's deployment guide specifically calls out disabling Printer Capture when deploying in a server environment such as Remote Desktop Services. |
| Disable Snagit Library Location Change | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!DataStoreNoMove` = 1 | Enabled | Prevents users on shared or non-persistent hosts from redirecting the Snagit Library to a path that won't exist on their next session, which TechSmith recommends to avoid data loss. |
| Disable Snagit Library Location Change | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!DataStoreNoMove` = 1 | Enabled | Prevents users on shared or non-persistent hosts from redirecting the Snagit Library to a path that won't exist on their next session, which TechSmith recommends to avoid data loss. |
| Disable Automatic Updates | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!NoAutoUpdateSupport` = 1 | Enabled | Keeps every session host on the same Snagit version instead of individual sessions triggering updates independently. |
| Disable Automatic Updates | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!NoAutoUpdateSupport` = 1 | Enabled | Keeps every session host on the same Snagit version instead of individual sessions triggering updates independently. |

