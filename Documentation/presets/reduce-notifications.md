# Reduce Notifications & Prompts

Suppresses the post-install registration dialog and in-product promotional/help notification banners, for organizations that deploy a site key centrally and don't want users seeing registration or upsell-style prompts.

Ready-to-import registry file: [`Presets/ReduceNotifications.reg`](../../Presets/ReduceNotifications.reg) - no Group Policy infrastructure required. For centrally managed deployment, set the matching policy under **Computer Configuration \ Administrative Templates \ TechSmith Snagit** (or **User Configuration** for user-scope policies) instead.

| Policy | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|
| Disable Online Registration Dialog | Machine | `NoRegistrationDialogSupport` = 1 | Enabled | TechSmith's own guidance: once a software key is deployed via the Deployment Tool, the online registration dialog should be disabled to prevent user-entered errors or duplicate registration. |
| Allow In-Product Notifications | Machine | `TSC_IN_PRODUCT_NOTIFICATIONS_ALLOW` = 0 | Disabled | Turns off in-product help and promotional notification banners directly. |
| Allow Anonymous Usage Reporting (TUDI) | User | `TUDIOptedIn` = 0 | Disabled | TechSmith documents that disabling anonymous usage reporting also prevents contextual onboarding and help notification content from being shown, so this compounds the notification reduction. |

