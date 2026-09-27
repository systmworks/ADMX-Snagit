# Privacy Hardening

Minimizes what Snagit sends outside the organization's network and keeps updates under administrative control, for environments with internet security restrictions or data-egress policies.

Ready-to-import registry file: [`Presets/PrivacyHardening.reg`](../../Presets/PrivacyHardening.reg) - no Group Policy infrastructure required. For centrally managed deployment, set the matching policy under **Computer Configuration \ Administrative Templates \ TechSmith Snagit** (or **User Configuration** for user-scope policies) instead.

| Policy | Scope | Registry Value | Set To | Why |
|---|---|---|---|---|
| Allow Anonymous Usage Reporting (TUDI) | User | `TUDIOptedIn` = 0 | Disabled | Stops Snagit from sending anonymous usage telemetry to TechSmith. TechSmith explicitly calls this option out for organizations that must follow internet security requirements restricting data sent externally. |
| Disable Online Registration Dialog | Machine | `NoRegistrationDialogSupport` = 1 | Enabled | Avoids sending registration information to TechSmith's online registration service; pair with a centrally deployed site key. |
| Disable Automatic Updates | Machine | `NoAutoUpdateSupport` = 1 | Enabled | Stops Snagit from reaching out over BITS to check for and fetch new versions; update timing becomes an administrative decision instead of automatic outbound traffic. |

