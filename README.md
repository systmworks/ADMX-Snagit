<p align="center"><a href="https://buymeacoffee.com/systmworks"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" height="45" alt="Buy me a coffee"></a></p>

> I have spent many, many hours creating and testing this ADMX. If it helps you please consider buying me a Coffee :)

# Snagit ADMX/ADML

Group Policy administrative template for **TechSmith Snagit 2025 / 2026** on Windows, built for **uniform policy enforcement across an entire enterprise device fleet** - not just a one-time configuration applied at install. A single `Snagit.admx` / `Snagit.adml` pair covers machine-level (`HKLM`) and user-level (`HKCU`) settings.

| ![File](https://img.shields.io/badge/File-316dca?style=flat-square) | ![Scope](https://img.shields.io/badge/Scope-316dca?style=flat-square) | ![Policies](https://img.shields.io/badge/Policies-316dca?style=flat-square) |
|------|-------|----------|
| `Snagit.admx` + ADML | **Snagit** (Computer + User) | 13 (12 machine + 1 user) |

## Why an ADMX, not just the Deployment Tool

TechSmith's own Deployment Tool (MST) and Snagit.reg import are **install-time only**: they write a set of starting values into the registry when Snagit is installed, and from that point on Snagit just uses whatever is left in that registry key - on that one machine. There's no mechanism to keep hundreds or thousands of endpoints in the same state afterward.

An ADMX/ADML template is built for exactly that: **enforcing a uniform configuration across the whole fleet, continuously, for as long as devices are joined to the policy** - not a snapshot taken once at install time.

- **Enforced continuously, not just at install.** Group Policy re-applies every setting in this template on each policy refresh - at logon, on the standard background refresh cycle (every 90-120 minutes by default), and on `gpupdate /force`. If a user changes a locked-down setting, or a re-image or repair resets it, the next refresh across the fleet puts it back automatically. A one-off MST can't do that.
- **One change, fleet-wide.** Flip a policy in a single GPO (or Intune configuration profile) and every targeted device - one or ten thousand - picks it up on its next refresh. No rebuilding an MST, no re-running the Deployment Tool, no touching individual machines.
- **Every device stays in the same state, indefinitely.** New machines joining the fleet, re-imaged machines, and machines that have been running for years all converge on the same configuration, without anyone tracking which install used which MST.
- **Works without touching the Snagit installation at all.** Because the template manages Snagit's real settings keys directly, you can change organization-wide behavior at any time after rollout, independent of install/upgrade cycles.
- **Lives alongside every other application policy you manage.** Shows up natively in Group Policy Management, the Local Group Policy Editor, and Intune - the same tools and workflow admins already use to enforce fleet-wide policy for Office, Chrome, Edge, etc. No separate deployment-tool UI to learn or keep installed.
- **Auditable and reviewable.** Settings live in a GPO/Intune profile you can inspect, diff, and roll back like any other enterprise policy, rather than being baked once into a per-deployment MST file that's easy to lose track of across a large fleet.

## Source of truth

Snagit has no dedicated Group Policy "Policies" registry branch. TechSmith documents the settings the application itself reads from `HKLM\SOFTWARE\TechSmith\Snagit` and `HKCU\SOFTWARE\TechSmith\Snagit` in its own enterprise deployment guides, and this template targets exactly those keys - nothing invented or scraped from unofficial sources:

- **Snagit MSI Installation Guide**, version 2026.0.0 (January 2026) - TechSmith Corporation
- **Snagit 2025 Deployment Tool Guide**, Deployment Tool v6.7.0 (August 2025) - TechSmith Corporation

Both are published on TechSmith's [Software Deployment & Resources](https://www.techsmith.com/) pages for Snagit administrators.

> [!IMPORTANT]
> This template does **not** include a license/registration key policy. Product keys should always be your own organization's legitimately purchased license, deployed through TechSmith's own **Deployment Tool** (which generates an MST with the `TSC_SOFTWARE_KEY` property) - not hardcoded into a redistributed template. See [TechSmith's Software Key FAQ](https://support.techsmith.com/hc/en-us/articles/204623018) if you need to locate your organization's key.

## Category overview

| ![Category](https://img.shields.io/badge/Category-316dca?style=flat-square) | ![Description](https://img.shields.io/badge/Description-316dca?style=flat-square) | ![Scope](https://img.shields.io/badge/Scope-316dca?style=flat-square) |
|------|-------------|:---:|
| Capture Options | Disable image, video, or printer capture. | Machine |
| Automatic Updates | Disable auto-update checks; configure a BITS proxy list for the updater. | Machine |
| Privacy and Notifications | Disable the online registration dialog; allow/deny in-product notifications; allow/deny anonymous usage reporting (TUDI). | Machine + User |
| Storage Locations | Lock or redirect the Snagit Library (auto-stored captures), user settings path, and outputs install path. | Machine |
| Virtualization | Force or auto-detect Snagit's VM/Remote Desktop display optimizations. | Machine |

## Policy reference

| Policy | Scope | Registry value | Type |
|---|---|---|---|
| Disable Image Capture | Machine | `NoImageCaptureSupport` | DWORD |
| Disable Video Capture | Machine | `NoVideoCaptureSupport` | DWORD |
| Disable Printer Capture | Machine | `NoPrinterCaptureSupport` | DWORD |
| Disable Automatic Updates | Machine | `NoAutoUpdateSupport` | DWORD |
| Configure Auto-Update Proxy Server List | Machine | `UpdaterProxyList` | REG_SZ |
| Disable Online Registration Dialog | Machine | `NoRegistrationDialogSupport` | DWORD |
| Allow In-Product Notifications | Machine | `TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` | DWORD |
| Allow Anonymous Usage Reporting (TUDI) | User | `TUDIOptedIn` | DWORD |
| Disable Snagit Library Location Change | Machine | `DataStoreNoMove` | DWORD |
| Set Snagit Library (Auto-Stored Files) Location | Machine | `DataStoreLocation` | REG_SZ |
| Set User Settings (AppData) Path | Machine | `AppDataPath` | REG_SZ |
| Set Snagit Outputs Install Path | Machine | `AccessoryInstallPath` | REG_SZ |
| Set Virtual Machine Display Optimization | Machine | `VMOverride` | REG_SZ (`None` / `AutoDetect` / `OtherVM`) |

All machine-scope values live under `HKEY_LOCAL_MACHINE\SOFTWARE\TechSmith\Snagit`; the one user-scope value lives under `HKEY_CURRENT_USER\SOFTWARE\TechSmith\Snagit`. These are Snagit's own live settings keys, so Group Policy simply manages the same values the Snagit UI and the TechSmith Deployment Tool would otherwise write.

## Curated presets

Beyond the raw policy list, [`Documentation/presets/`](Documentation/presets/) has ready-made bundles for common deployment scenarios, each with a matching double-click `.reg` file under [`Presets/`](Presets/) for a quick single-machine test - though for actual fleet-wide enforcement, use the equivalent policies in `Snagit.admx` via Group Policy or Intune rather than the `.reg` files, so the settings are reapplied automatically instead of being a one-time write.

| ![Preset](https://img.shields.io/badge/Preset-316dca?style=flat-square) | ![Scenario](https://img.shields.io/badge/Scenario-316dca?style=flat-square) |
|------|-------------|
| [Reduce Notifications & Prompts](Documentation/presets/reduce-notifications.md) | Suppresses the registration dialog and in-product promotional/help banners for centrally-licensed deployments. |
| [Privacy Hardening](Documentation/presets/privacy-hardening.md) | Turns off anonymous usage reporting and puts update timing and registration under IT control, for data-egress-restricted environments. |
| [RDS / VDI Deployment](Documentation/presets/rds-vdi-deployment.md) | Applies TechSmith's own virtualization optimizations, disables printer capture, and locks the library path for Remote Desktop Services / non-persistent VDI hosts. |

Full index: [Documentation/presets/README.md](Documentation/presets/README.md).

## Installation

1. Copy `policyDefinitions\Snagit.admx` to your PolicyDefinitions store:
   - Central Store: `\\<domain>\SYSVOL\<domain>\Policies\PolicyDefinitions\`
   - Local machine (single PC / testing): `C:\Windows\PolicyDefinitions\`
2. Copy `policyDefinitions\en-US\Snagit.adml` to the matching `en-US` subfolder in the same location.
3. Open Group Policy Management Editor (or the Local Group Policy Editor) - the settings appear under **Computer Configuration \ Administrative Templates \ TechSmith Snagit** and **User Configuration \ Administrative Templates \ TechSmith Snagit**.
4. Configure the policies you need, then deploy the GPO as normal.

For Intune, upload `Snagit.admx` and `Snagit.adml` together as an [ADMX ingestion / imported administrative template](https://learn.microsoft.com/mem/intune/configuration/administrative-templates-import-custom).

Don't have Group Policy or Intune at all? Skip both and double-click one of the `.reg` files under [`Presets/`](Presets/) instead - see [Curated presets](#curated-presets) above.

---

**Sharing & responsibility** - Built for the community, shared with good intentions. Use at your own risk. The author accepts no responsibility for any outcomes resulting from the use of these files. Always verify registry paths and values, and test in a safe environment first. If you find an issue or have a suggestion, contributions are welcome.
