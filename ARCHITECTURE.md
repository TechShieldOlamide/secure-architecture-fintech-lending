# Architecture & Data Flow

## System Architecture

```mermaid
graph TB
    subgraph Client Layer
        WebApp[Web App]
        MobileApp[Mobile App]
    end

    subgraph Edge
        Gateway[API Gateway / Load Balancer]
    end

    subgraph Core Services
        Auth[Auth Service<br/>OAuth2 + MFA]
        Lending[Loan Origination &<br/>Underwriting Service]
        CustomerData[Customer Data Service<br/>PII store]
        Payments[Payment Service]
        Notify[Notification Service<br/>SMS / Email OTP]
        Admin[Multi-Tenant Admin<br/>Dashboard]
    end

    subgraph Data Layer
        DB[(Primary Database<br/>Tenant-isolated schemas)]
        DocStore[(Encrypted Object Storage<br/>KYC documents)]
        Logs[(Centralized Audit Log /<br/>SIEM)]
    end

    subgraph Third-Party Integrations
        KYCProvider[KYC / ID Verification Provider]
        CreditBureau[Credit Bureau API]
        PaymentProcessor[Payment Processor]
    end

    WebApp --> Gateway
    MobileApp --> Gateway
    Gateway --> Auth
    Gateway --> Lending
    Gateway --> Admin

    Auth --> DB
    Lending --> CustomerData
    Lending --> DB
    Lending --> CreditBureau
    CustomerData --> DB
    CustomerData --> DocStore
    CustomerData --> KYCProvider
    Payments --> PaymentProcessor
    Payments --> DB
    Lending --> Payments
    Lending --> Notify
    Payments --> Notify

    Auth -.audit.-> Logs
    Lending -.audit.-> Logs
    CustomerData -.audit.-> Logs
    Payments -.audit.-> Logs
    Admin -.audit.-> Logs
```

## Data Flow: Loan Application to Disbursement

```mermaid
sequenceDiagram
    participant U as Customer
    participant G as API Gateway
    participant A as Auth Service
    participant L as Lending Service
    participant CD as Customer Data Service
    participant KYC as KYC Provider (3rd party)
    participant CB as Credit Bureau (3rd party)
    participant P as Payment Service
    participant PP as Payment Processor (3rd party)

    U->>G: Submit loan application (PII + financial data)
    G->>A: Authenticate request
    A-->>G: Token verified
    G->>L: Forward application
    L->>CD: Store/retrieve customer PII
    CD->>KYC: Submit ID document for verification
    KYC-->>CD: Verification result
    L->>CB: Request credit check
    CB-->>L: Credit report
    L->>L: Underwriting decision
    alt Approved
        L->>P: Trigger disbursement
        P->>PP: Initiate transfer
        PP-->>P: Confirmation
        P-->>U: Notify: funds disbursed
    else Declined
        L-->>U: Notify: application declined
    end
```

## Reasoning

**Why route everything through a single API Gateway?** It gives one enforcement point for authentication, rate limiting, and request logging, rather than each service needing to independently implement these, which is where inconsistencies (and security gaps) tend to creep in.

**Why is Customer Data Service separated from Lending Service?** PII and KYC documents have different sensitivity and different compliance obligations (data subject rights, retention limits) than transactional loan data. Separating them means access to one doesn't automatically grant access to the other, and each can have its own, narrower access control policy.

**Why does Payment Service never talk to Customer Data Service directly?** Payment processing only needs a disbursement account reference, not full customer PII. This is a data minimization decision: the service with the widest external exposure (talking to a third-party payment processor) has the narrowest possible view of sensitive data.

**Why is there a centralized audit log fed by every service?** Multi-tenant systems need a single place to answer "who accessed what, when" across tenant boundaries, especially important for incident response and for demonstrating control effectiveness to an auditor.
