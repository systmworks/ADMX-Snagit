# Privacy Hardening

Minimizes what Snagit sends outside the organization's network and keeps updates under administrative control, for environments with internet security restrictions or data-egress policies.

Ready-to-import registry file: [`Presets/PrivacyHardening.reg`](../../Presets/PrivacyHardening.reg) - covers both Snagit 2025 and Snagit 2026 in one file; no Group Policy infrastructure required. For centrally managed deployment, set the matching policy for your Snagit version under **Computer Configuration \ Administrative Templates \ Snagit \ Snagit 2025 (or 2026)** (or **User Configuration** for user-scope policies) instead.

| Policy | Snagit Version | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|---|
| Allow Anonymous Usage Reporting (TUDI) | 2025 | User | `SOFTWARE\TechSmith\Snagit\25!TUDIOptedIn` = 0 | Disabled | Stops Snagit from sending anonymous usage telemetry to TechSmith. TechSmith explicitly calls this option out for organizations that must follow internet security requirements restricting data sent externally. |
| Allow Anonymous Usage Reporting (TUDI) | 2026 | User | `SOFTWARE\TechSmith\Snagit!TUDIOptedIn` = 0 | Disabled | Stops Snagit from sending anonymous usage telemetry to TechSmith. TechSmith explicitly calls this option out for organizations that must follow internet security requirements restricting data sent externally. |
| Disable Online Registration Dialog | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!NoRegistrationDialogSupport` = 1 | Enabled | Avoids sending registration information to TechSmith's online registration service; pair with a centrally deployed site key. |
| Disable Online Registration Dialog | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!NoRegistrationDialogSupport` = 1 | Enabled | Avoids sending registration information to TechSmith's online registration service; pair with a centrally deployed site key. |
| Disable Automatic Updates | 2025 | Machine | `SOFTWARE\TechSmith\Snagit\25!NoAutoUpdateSupport` = 1 | Enabled | Stops Snagit from reaching out over BITS to check for and fetch new versions; update timing becomes an administrative decision instead of automatic outbound traffic. |
| Disable Automatic Updates | 2026 | Machine | `SOFTWARE\TechSmith\Snagit!NoAutoUpdateSupport` = 1 | Enabled | Stops Snagit from reaching out over BITS to check for and fetch new versions; update timing becomes an administrative decision instead of automatic outbound traffic. |

