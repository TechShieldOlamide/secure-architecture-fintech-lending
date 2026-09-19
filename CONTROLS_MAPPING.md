# Controls Mapping

Each threat identified in `THREAT_MODEL.md` is mapped here to the specific ISO/IEC 27001:2022 Annex A control (and, where relevant, the corresponding SOC 2 Trust Services Criterion) that mitigates it. This is deliberately not a generic "here's what ISO 27001 requires" list, each row traces back to a specific, named threat against a specific component.

| Threat | ISO/IEC 27001:2022 Annex A Control | SOC 2 TSC (if applicable) | How it mitigates the threat |
|---|---|---|---|
| T-01 (Gateway spoofing) | A.8.5 Secure authentication | CC6.1 | Requires strong, verifiable authentication at the point of entry, preventing forged requests from reaching backend services unauthenticated. |
| T-02 (Gateway DoS) | A.8.6 Capacity management | CC7.2 | Requires monitoring and managing resource capacity, the basis for rate limiting and autoscaling that prevent a single source from degrading service for all tenants. |
| T-03 (Auth credential attack) | A.8.5 Secure authentication (MFA specifically) | CC6.1 | Mandates authentication mechanisms resistant to credential-based attacks; MFA directly closes the gap a password-only login leaves open. |
| T-04 (Token cross-tenant elevation) | A.8.3 Information access restriction | CC6.3 | Requires access to information to be restricted according to the access control policy, tenant-scoped token validation is the technical enforcement of this control. |
| T-05 (Cross-tenant PII disclosure) | A.8.3 Information access restriction | CC6.1 | Same control, applied at the data layer: query-level tenant isolation is what makes this control real rather than aspirational. |
| T-06 (PII tampering without audit) | A.8.15 Logging | CC7.2 | Requires logging of relevant events; an audit trail on every write to customer PII is what makes unauthorized tampering detectable after the fact. |
| T-07 (Vendor-side PII exposure) | A.5.19 Information security in supplier relationships | CC9.2 | Requires assessing and managing security risk introduced by suppliers, the basis for due diligence on the KYC provider's own security posture before integration. |
| T-08 (No record of KYC exchange) | A.8.15 Logging | CC7.2 | Requires that significant events, including third-party data exchanges, are logged and retrievable. |
| T-09 (Disbursement tampering in transit) | A.8.24 Use of cryptography | CC6.7 | Requires encryption of data in transit, preventing interception or modification of a disbursement request between LendSecure and the payment processor. |
| T-10 (Disbursement not traceable to approval) | A.8.15 Logging | CC7.2 | Same logging control as T-06/T-08, applied to link every financial transaction to its originating authorization decision. |
| T-11 (Admin cross-tenant visibility) | A.8.3 Information access restriction | CC6.3 | Same access restriction control as T-04/T-05, applied specifically to the admin interface, which needs its own explicit scoping, not just inherited API-level controls. |
| T-12 (Admin session hijacking) | A.8.5 Secure authentication | CC6.1 | Requires secure session handling as part of authentication; short session lifetimes and re-authentication for sensitive admin actions reduce this risk. |
| T-13 (Storage misconfiguration) | A.8.9 Configuration management | CC6.1 | Requires a defined, monitored process for managing secure configuration of infrastructure, catching storage permission misconfigurations before they become exploitable. |

## Notes on this mapping approach

A few threats map to the same underlying control (for example, T-04, T-05, and T-11 all trace back to A.8.3, Information access restriction). This is intentional and realistic: a single well-implemented control, applied consistently across every layer where tenant data is touched, is what actually prevents the platform's single biggest risk, cross-tenant data exposure, rather than treating each layer as needing its own bespoke solution.
