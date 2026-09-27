<p align="center"><a href="https://buymeacoffee.com/systmworks"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" height="45" alt="Buy me a coffee"></a></p>

> I have spent many, many hours creating and testing this ADMX. If it helps you please consider buying me a Coffee :)

> [!TIP]
> **Coming soon: ADMX Pro.** Ready-to-import Intune & GPO profiles (Security Hardened · STIG-aligned · No Nags & Upsells),
> re-tested against every vendor release, plus an email alert when a vendor changes or breaks a setting.
> **[Join the waitlist →](https://tally.so/r/7RGrVR)** (free, no spam, one email when it launches)

# Snagit ADMX/ADML

Group Policy administrative template for **TechSmith Snagit 2025 and 2026** on Windows, built for **uniform policy enforcement across an entire enterprise device fleet** - not just a one-time configuration applied at install. A single `Snagit.admx` / `Snagit.adml` pair covers machine-level (`HKLM`) and user-level (`HKCU`) settings for **both** Snagit versions, kept as separate policy sets because the two versions store their settings under different registry keys (see [Snagit 2025 vs Snagit 2026](#snagit-2025-vs-snagit-2026---pick-the-right-category) below).

| ![File](https://img.shields.io/badge/File-316dca?style=flat-square) | ![Scope](https://img.shields.io/badge/Scope-316dca?style=flat-square) | ![Policies](https://img.shields.io/badge/Policies-316dca?style=flat-square) |
|------|-------|----------|
| `Snagit.admx` + ADML | **Snagit 2025** + **Snagit 2026** (Computer + User) | 26 (24 machine + 2 user - 13 settings x 2 versions) |

## Why an ADMX, not just the Deployment Tool

TechSmith's own Deployment Tool (MST) and Snagit.reg import are **install-time only**: they write a set of starting values into the registry when Snagit is installed, and from that point on Snagit just uses whatever is left in that registry key - on that one machine. There's no built-in mechanism to keep hundreds or thousands of endpoints in the same state afterward, or to change a setting fleet-wide without re-touching every install.

- **One change, fleet-wide, without rebuilding anything.** Edit a policy in a single GPO (or Intune configuration profile) and every targeted device picks it up - no rebuilding an MST, no re-running the Deployment Tool, no touching individual machines.
- **Every device converges on the same configuration.** New machines joining the fleet, re-imaged machines, and long-running machines all end up managed the same way, without anyone tracking which install used which MST version.
- **Re-applied whenever the policy is processed** - at logon/startup, whenever the GPO itself changes, on `gpupdate /force`, and (for Intune) on the client's periodic policy check-in. See the important caveat below on what this does and doesn't guarantee between those points.
- **Works without touching the Snagit installation at all.** Because the template manages Snagit's real settings keys directly, you can change organization-wide behavior at any time after rollout, independent of install/upgrade cycles.
- **Lives alongside every other application policy you manage.** Shows up natively in Group Policy Management, the Local Group Policy Editor, and Intune - the same tools and workflow admins already use for Office, Chrome, Edge, etc. No separate deployment-tool UI to learn or keep installed.
- **Auditable and reviewable.** Settings live in a GPO/Intune profile you can inspect, diff, and roll back like any other enterprise policy, rather than being baked once into a per-deployment MST file that's easy to lose track of across a large fleet.

> [!IMPORTANT]
> **This is not the protected `HKLM\SOFTWARE\Policies\` branch - read this before relying on it for lockdown.** Snagit has no dedicated Group Policy "Policies" registry area, so this template manages Snagit's own live settings keys directly (see [Source of truth](#source-of-truth)). Two consequences:
> - **Group Policy's default background refresh does not blindly re-write these values on every cycle.** By default, the Registry client-side extension skips reprocessing a GPO whose version hasn't changed since the last cycle. Values here get reasserted at logon/startup, whenever you actually edit the GPO (which bumps its version), on `gpupdate /force`, or fleet-wide if you enable **Computer Configuration → Administrative Templates → System → Group Policy → "Configure registry policy processing" → Process even if the Group Policy objects have not changed**. Intune's periodic device check-in re-applies its configuration profiles on its own schedule regardless, which is closer to "continuously enforced" than default GPO behavior.
> - **Values persist ("tattoo") after the policy is removed.** Unlinking the GPO or removing the Intune profile does not revert Snagit to its defaults - the last-written value stays in the registry until something else changes it.
> - **The two per-user settings (`TUDIOptedIn` for each version) are ordinary Snagit preferences**, not locked values - the signed-in user can change them themselves in Snagit's own settings at any time, and they will only be corrected back the next time this policy is actually (re)processed, not continuously.

## Snagit 2025 vs Snagit 2026 - pick the right category

**Snagit changed where it stores its settings between the 2025 and 2026 releases**, confirmed by directly inspecting the Registry table embedded in both installers (see [Verification status](#verification-status)):

| Snagit version | Confirmed from build | Registry key |
|---|---|---|
| **Snagit 2025** | 25.4.1 | `SOFTWARE\TechSmith\Snagit\25` (versioned subfolder) |
| **Snagit 2026** | 26.3.1 | `SOFTWARE\TechSmith\Snagit` (unversioned) |

Because of this, every setting in this template exists **twice** - once under the **Snagit 2025** category and once under the **Snagit 2026** category, each pointed at the correct registry key for that version. **Configure the category that matches the Snagit version actually installed on the target machines; the other category's policies will silently do nothing if applied to the wrong version**, since Snagit only reads its own version's key.

If your fleet runs a mix of Snagit 2025 and 2026, target each with its own GPO/Intune profile (e.g. scoped by an installed-application WMI filter or an Intune app-detection rule), each configuring only its matching category.

## Source of truth

Snagit has no dedicated Group Policy "Policies" registry branch. TechSmith documents the settings the application itself reads from its own registry key in a set of enterprise deployment guides, and - where those guides turned out to be wrong or to disagree with each other - this template instead follows what TechSmith's own installers (MSI) actually ship, confirmed by directly querying each installer's embedded Registry table. Nothing here is invented or scraped from unofficial/forum sources. Direct links to every document used:

- [Snagit MSI Installation Guide, version 2026.0.0](https://www.techsmith.com/wp-content/uploads/2026/02/Snagit-MSI-Installation-Guide.pdf) (January 2026) - TechSmith Corporation
- [Snagit 2025 MSI Installation Guide](https://www.techsmith.com/wp-content/uploads/2025/08/Snagit-MSI-Installation-Guide.pdf) (August 2025) - TechSmith Corporation - a distinct, separately-versioned document from the 2026.0.0 guide above
- [Snagit 2025 Deployment Tool Guide, v6.7.0](https://www.techsmith.com/wp-content/uploads/2025/08/Snagit-Deployment-Tool-Guide.pdf) (August 2025) - TechSmith Corporation
- [Install Snagit on Remote Desktop Services, version 2026.2.1](https://www.techsmith.com/wp-content/uploads/2026/05/Install-Snagit-on-Remote-Desktop-Services.pdf) (May 2026) - TechSmith Corporation
- The Registry and Property tables embedded in the official Snagit installers themselves: `snagit.msi` build **26.3.1** (current Snagit 2026 release) and build **25.4.1** (Snagit 2025 release branch), both downloaded directly from TechSmith's own release CDN and queried read-only via the Windows Installer API - no installation performed

> [!NOTE]
> **Where TechSmith's own guides disagreed, and how it was resolved.** The 2025 MSI Installation Guide's registry examples use `HKEY_LOCAL_MACHINE\Software\TechSmith\Snagit\25\`; the 2026.0.0 guide and the May 2026 RDS guide both use the unversioned path instead. Querying the actual installers directly confirmed the 2025 guide's *path* is correct for Snagit 2025 - but also caught a **separate** error: that same 2025 guide's prose lists the Library-location value as `DataStoreLocation`, when the real installer (both 2025 and 2026) ships it as `LibraryStoreDirectory`. Both corrections are reflected in this template; see [Verification status](#verification-status) for the full list of what was checked this way.

> [!IMPORTANT]
> This template does **not** include a license/registration key policy. Product keys should always be your own organization's legitimately purchased license, deployed through TechSmith's own **Deployment Tool** (which generates an MST with the `TSC_SOFTWARE_KEY` property) - not hardcoded into a redistributed template. See [TechSmith's Software Key FAQ](https://support.techsmith.com/hc/en-us/articles/204623018) if you need to locate your organization's key.

## Category overview

Each of the following exists once under **Snagit 2025** and once under **Snagit 2026**:

| ![Category](https://img.shields.io/badge/Category-316dca?style=flat-square) | ![Description](https://img.shields.io/badge/Description-316dca?style=flat-square) | ![Scope](https://img.shields.io/badge/Scope-316dca?style=flat-square) |
|------|-------------|:---:|
| Capture Options | Disable image, video, or printer capture. | Machine |
| Automatic Updates | Disable auto-update checks; configure a BITS proxy list for the updater. | Machine |
| Privacy and Notifications | Disable the online registration dialog; allow/deny in-product notifications; allow/deny anonymous usage reporting (TUDI). | Machine + User |
| Storage Locations | Lock or redirect the Snagit Library (auto-stored captures), user settings path, and outputs install path. | Machine |
| Virtualization | Force or auto-detect Snagit's VM/Remote Desktop display optimizations. | Machine |

## Policy reference

Registry value names are identical between versions - only the base key differs (`SOFTWARE\TechSmith\Snagit\25` for 2025, `SOFTWARE\TechSmith\Snagit` for 2026, both under `HKEY_LOCAL_MACHINE` except the one `HKEY_CURRENT_USER` setting noted below).

| Policy | Scope | Registry value | Type |
|---|---|---|---|
| Disable Image Capture | Machine | `NoImageCaptureSupport` | DWORD |
| Disable Video Capture | Machine | `NoVideoCaptureSupport` | DWORD |
| Disable Printer Capture | Machine | `NoPrinterCaptureSupport` | DWORD |
| Disable Automatic Updates | Machine | `NoAutoUpdateSupport` | DWORD |
| Configure Auto-Update Proxy Server List | Machine | `UpdateProxyList` | REG_SZ |
| Disable Online Registration Dialog | Machine | `NoRegistrationDialogSupport` | DWORD |
| Allow In-Product Notifications | Machine | `TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` | DWORD |
| Allow Anonymous Usage Reporting (TUDI) | **User** | `TUDIOptedIn` | DWORD |
| Disable Snagit Library Location Change | Machine | `DataStoreNoMove` | DWORD |
| Set Snagit Library (Auto-Stored Files) Location | Machine | `LibraryStoreDirectory` | REG_SZ |
| Set User Settings (AppData) Path | Machine | `AppDataPath` | REG_SZ |
| Set Snagit Outputs Install Path | Machine | `AccessoryInstallPath` | REG_SZ |
| Set Virtual Machine Display Optimization | Machine | `VMOverride` | REG_SZ (`None` / `AutoDetect` / `OtherVM`) |

These are Snagit's own live settings keys, so Group Policy simply manages the same values the Snagit UI and the TechSmith Deployment Tool would otherwise write.

## Verification status

Not every value here carries the same level of confidence. To be specific about what's been checked and how:

**Empirically confirmed** - checked directly against the Registry table embedded in the real Snagit installers (builds 25.4.1 and 26.3.1), independent of what TechSmith's written guides say:
- The registry key itself: versioned (`...\Snagit\25`) for 2025, unversioned (`...\Snagit`) for 2026
- `NoImageCaptureSupport`, `NoVideoCaptureSupport`, `NoPrinterCaptureSupport`, `NoAutoUpdateSupport`, `NoRegistrationDialogSupport`, `AppDataPath`, `VMOverride`, `DataStoreNoMove`, `TUDIOptedIn` (all identical between versions, differing only by base key)
- `LibraryStoreDirectory` - confirmed as the real value in both versions; TechSmith's own MSI guides print the older `DataStoreLocation` name in their prose, which does not match either shipping installer
- `UpdateProxyList` - confirmed as the real value in both versions; an earlier revision of this template used the guessed, incorrect name `UpdaterProxyList`, which does not exist in either installer

**Documented but not found in either installer's static Registry table** - these two settings aren't backed by a declarative MSI Property-to-registry mapping in either installer (which just means TechSmith implements them a different way internally, e.g. a custom action, or as a value the app reads without any installer-driven default at all - not that they're wrong):
- `AccessoryInstallPath` - documented in the MSI guides' "Features" list, and independently confirmed via a live regedit walkthrough in the May 2026 RDS deployment guide
- `TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` - documented only in the MSI guides' "Features" list (both the 2025 and 2026 editions, identical wording); never listed as an MSI Property at all, so there was never a Transform-driven path to check it against

**Not yet independently live-tested** - this template has been checked against TechSmith's documentation and the real installers' declarative install-time behavior, but not yet against a live install where every policy is set via actual Group Policy/Intune and the resulting Snagit behavior is observed directly. If you do this verification (e.g. in a Windows Sandbox or test VM against real Snagit 2025 and 2026 installs, or via a test Intune ADMX import), issues or confirmations are welcome.

## Curated presets

Beyond the raw policy list, [`Documentation/presets/`](Documentation/presets/) has ready-made bundles for common deployment scenarios, each with a matching double-click `.reg` file under [`Presets/`](Presets/) for a quick single-machine test. Each `.reg` file includes **both** the Snagit 2025 and Snagit 2026 registry sections - only the section matching the installed version actually takes effect, so you don't need to know the exact version up front for a one-off test. A `.reg` import is a one-time write with nothing to re-apply it later; for fleet-wide management where you want the settings centrally controlled and re-processed on the normal policy schedule, use the equivalent policies in `Snagit.admx` (under the category matching your Snagit version) via Group Policy or Intune instead.

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
3. Open Group Policy Management Editor (or the Local Group Policy Editor) - the settings appear under **Computer Configuration \ Administrative Templates \ Snagit** and **User Configuration \ Administrative Templates \ Snagit**, each with a **Snagit 2025** and a **Snagit 2026** sub-node.
4. Configure the policies under the sub-node matching the Snagit version actually installed on your target machines (see [Snagit 2025 vs Snagit 2026](#snagit-2025-vs-snagit-2026---pick-the-right-category)), then deploy the GPO as normal.

For Intune, upload `Snagit.admx` and `Snagit.adml` together as an [ADMX ingestion / imported administrative template](https://learn.microsoft.com/mem/intune/configuration/administrative-templates-import-custom).

Don't have Group Policy or Intune at all? Skip both and double-click one of the `.reg` files under [`Presets/`](Presets/) instead - see [Curated presets](#curated-presets) above.

---

**Sharing & responsibility** - Built for the community, shared with good intentions. Use at your own risk. The author accepts no responsibility for any outcomes resulting from the use of these files. Always verify registry paths and values, and test in a safe environment first. If you find an issue or have a suggestion, contributions are welcome.
