# RDS / VDI Deployment

Tuned for Remote Desktop Services, Citrix, or non-persistent VDI hosts: applies TechSmith's documented virtualization optimizations, removes the printer-capture destination, keeps the Snagit Library path fixed, and puts update timing under IT control.

Ready-to-import registry file: [`Presets/RdsVdiDeployment.reg`](../../Presets/RdsVdiDeployment.reg) - no Group Policy infrastructure required. For centrally managed deployment, set the matching policy under **Computer Configuration \ Administrative Templates \ TechSmith Snagit** (or **User Configuration** for user-scope policies) instead.

| Policy | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|
| Set Virtual Machine Display Optimization | Machine | `VMOverride` = `OtherVM` | OtherVM | TechSmith recommends always applying the virtualization display optimizations (OtherVM) when deploying with virtualization technologies, rather than relying on auto-detection. |
| Disable Printer Capture | Machine | `NoPrinterCaptureSupport` = 1 | Enabled | TechSmith's deployment guide specifically calls out disabling Printer Capture when deploying in a server environment such as Remote Desktop Services. |
| Disable Snagit Library Location Change | Machine | `DataStoreNoMove` = 1 | Enabled | Prevents users on shared or non-persistent hosts from redirecting the Snagit Library to a path that won't exist on their next session, which TechSmith recommends to avoid data loss. |
| Disable Automatic Updates | Machine | `NoAutoUpdateSupport` = 1 | Enabled | Keeps every session host on the same Snagit version instead of individual sessions triggering updates independently. |

