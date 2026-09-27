# Reduce Notifications & Prompts

Suppresses the post-install registration dialog and in-product promotional/help notification banners, for organizations that deploy a site key centrally and don't want users seeing registration or upsell-style prompts.

Ready-to-import registry file: [`Presets/ReduceNotifications.reg`](../../Presets/ReduceNotifications.reg) - covers both Snagit 2025 and Snagit 2026 in one file; no Group Policy infrastructure required. For centrally managed deployment, set the matching policy for your Snagit version under **Computer Configuration \ Administrative Templates \ Snagit \ Snagit 2025 (or 2026)** (or **User Configuration** for user-scope policies) instead.

| Policy | Snagit Version | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|---|
| Disable Online Registration Dialog | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!NoRegistrationDialogSupport` = 1 | Enabled | TechSmith's own guidance: once a software key is deployed via the Deployment Tool, the online registration dialog should be disabled to prevent user-entered errors or duplicate registration. |
| Disable Online Registration Dialog | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!NoRegistrationDialogSupport` = 1 | Enabled | TechSmith's own guidance: once a software key is deployed via the Deployment Tool, the online registration dialog should be disabled to prevent user-entered errors or duplicate registration. |
| Allow In-Product Notifications | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` = 0 | Disabled | Turns off in-product help and promotional notification banners directly. |
| Allow In-Product Notifications | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` = 0 | Disabled | Turns off in-product help and promotional notification banners directly. |
| Allow Anonymous Usage Reporting (TUDI) | 2025 | User | `SOFTWARE\TechSmith\Snagit\25!TUDIOptedIn` = 0 | Disabled | TechSmith documents that disabling anonymous usage reporting also prevents contextual onboarding and help notification content from being shown, so this compounds the notification reduction. |
| Allow Anonymous Usage Reporting (TUDI) | 2026 | User | `SOFTWARE\TechSmith\Snagit!TUDIOptedIn` = 0 | Disabled | TechSmith documents that disabling anonymous usage reporting also prevents contextual onboarding and help notification content from being shown, so this compounds the notification reduction. |

