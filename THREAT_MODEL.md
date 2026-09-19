# Threat Model (STRIDE)

Each major component from `ARCHITECTURE.md` is analyzed against the six STRIDE threat categories: **S**poofing, **T**ampering, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**levation of privilege. Only threats judged realistic and relevant to this architecture are listed, this is not an exhaustive checklist exercise.

## API Gateway

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-01 | Spoofing | An attacker forges a request to appear as a legitimate tenant or user without valid credentials. | Unauthorized access to another tenant's data. |
| T-02 | Denial of Service | High volume of requests from a single source degrades service for all tenants. | Platform-wide availability impact. |

## Auth Service

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-03 | Spoofing | Credential stuffing or brute-force attack against login, especially without MFA enforced. | Account takeover, leading to unauthorized access to that user's tenant data. |
| T-04 | Elevation of Privilege | A token issued for one tenant's scope is reused or manipulated to access another tenant's resources. | Cross-tenant data breach, the most severe realistic threat in a multi-tenant system. |

## Customer Data Service (PII store)

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-05 | Information Disclosure | A query or API flaw allows retrieval of PII belonging to a different tenant than the requester. | Regulatory breach (NDPA/GDPR-equivalent), reputational damage, mandatory breach notification. |
| T-06 | Tampering | Customer PII or KYC status is altered without authorization or audit trail. | Fraudulent loan approval, data integrity loss. |

## KYC Provider Integration (third party)

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-07 | Information Disclosure | ID documents transmitted to or stored by the third-party KYC provider are exposed via a vulnerability on their side, outside LendSecure's direct control. | PII exposure attributable to a vendor, still a data protection obligation for LendSecure as the data controller. |
| T-08 | Repudiation | No reliable record exists of what was sent to or received from the KYC provider for a given verification. | Inability to prove verification occurred correctly during an audit or dispute. |

## Payment Service & Payment Processor Integration

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-09 | Tampering | A disbursement request is intercepted or modified in transit, changing the destination account or amount. | Direct financial loss. |
| T-10 | Repudiation | A completed disbursement cannot be definitively tied to the specific loan approval that authorized it. | Fraud investigation and audit difficulty. |

## Multi-Tenant Admin Dashboard

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-11 | Elevation of Privilege | An admin user for one tenant gains visibility into another tenant's data through an under-scoped query or UI flaw. | Cross-tenant data breach. |
| T-12 | Spoofing | Session hijacking of an admin session grants broad access across that tenant's full customer base. | Large-scale data exposure for a single tenant. |

## Encrypted Object Storage (KYC documents)

| Threat | Category | Description | Impact |
|---|---|---|---|
| T-13 | Information Disclosure | Misconfigured storage permissions make documents accessible without going through the application's access control layer. | Direct PII/document exposure, a common real-world cause of cloud data breaches. |
