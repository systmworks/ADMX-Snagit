# Curated Presets

Each preset bundles a handful of the Snagit ADMX policies for a common deployment scenario. Every value is validated against the ADMX policy set before publishing, so a preset can never reference a setting or value that the ADMX itself does not support. Each `.reg` file includes both the Snagit 2025 and Snagit 2026 registry sections in one file - only the section matching the installed version takes effect.

Two ways to apply a preset:
1. **Standalone (no Group Policy):** double-click the matching `.reg` file in [`Presets/`](../../Presets/) on the target machine (or deploy it via login script / RMM).
2. **Centrally managed:** open the linked doc for the exact policies and values, then set them in Group Policy Management or Intune using `Snagit.admx`, under the category matching the installed Snagit version.

| Preset | Reg file | Policies |
|---|---|---|
| [Privacy Hardening](privacy-hardening.md) | [PrivacyHardening.reg](../../Presets/PrivacyHardening.reg) | 3 policies x 2 versions |
| [RDS / VDI Deployment](rds-vdi-deployment.md) | [RdsVdiDeployment.reg](../../Presets/RdsVdiDeployment.reg) | 4 policies x 2 versions |
| [Reduce Notifications & Prompts](reduce-notifications.md) | [ReduceNotifications.reg](../../Presets/ReduceNotifications.reg) | 3 policies x 2 versions |

