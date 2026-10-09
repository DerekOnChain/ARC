# ARC architecture and data flow

**Proposed design | Foundation configured PAUSED | October 9, 2026**

These diagrams depict intended relationships, not a deployed production environment. The complete foundation is the private instruction/context package; Site, backend, workers, CRM and providers remain pending.

## System architecture

Public and private interfaces share an authenticated API. Durable state belongs in the backend; authorized assistant tools read and change the same records used by the command center.

```mermaid
flowchart TD
    subgraph Interfaces["Public and private interfaces"]
        A["Public agency Site"]
        B["Private command center"]
        C["Private ARC assistant"]
    end
    A -->|Review request| D["Authenticated API boundary"]
    B -->|Owner and employee access| D
    C -->|Authorized tools| D
    D --> E["Durable CRM and settings"]
    D --> F["Budgeted job queue"]
    F --> G["Discovery and research workers"]
    G --> H["Evidence and report storage"]
    G --> E
    E --> I["Scoring and report services"]
    H --> I
    I --> D
    D --> J{"Owner approval and send gates"}
    J --> K["Email provider"]
    K -->|Status and response events| E
    L["Pause, cost ledger and audit log"] -.-> D
    L -.-> F
    L -.-> J
    classDef interface fill:#e0f7fa,stroke:#00838f,color:#073344;
    classDef core fill:#ede9fe,stroke:#7c3aed,color:#29184b;
    classDef control fill:#fff2d7,stroke:#b77900,color:#4c3300;
    class A,B,C interface;
    class D,E,F,G,H,I,K core;
    class J,L control;
```

## Research-to-outcome flow

Candidates pass eligibility, bounded screening and evidence-based scoring. Owner selection precedes reports/drafts. Approval, pause, budget and suppression controls are separate from score thresholds.

```mermaid
flowchart TD
    A["Authorized sources"] --> B["Normalize and deduplicate"]
    B --> C{"Eligible business?"}
    C -->|No| X["Exclude with reason"]
    C -->|Yes| D["Bounded website screening"]
    D --> E["Website and business evidence"]
    E --> F["Score with confidence"]
    F --> G{"Review threshold met?"}
    G -->|No| W["Watch or reject"]
    G -->|Yes| H["Owner selects lead"]
    H --> I["Private report and email draft"]
    I --> J{"Exact message approved?"}
    J -->|No| K["Revise or hold"]
    K --> I
    J -->|Yes| L["Final suppression and pause checks"]
    L --> M["Send when separately enabled"]
    M --> N["CRM outcomes and review"]
    N -.-> F
    P["Pause and budget gates"] -.-> D
    P -.-> E
    P -.-> M
    classDef source fill:#e0f7fa,stroke:#00838f,color:#073344;
    classDef core fill:#ede9fe,stroke:#7c3aed,color:#29184b;
    classDef control fill:#fff2d7,stroke:#b77900,color:#4c3300;
    classDef result fill:#dcfce7,stroke:#16804a,color:#073a23;
    class A,B source;
    class D,E,F,H,I core;
    class C,G,J,L,P control;
    class M,N result;
```

## Message approval lifecycle

This is the planned send lifecycle. The foundation has no sending integration. A score, draft or report never grants permission to send.

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> NeedsApproval: Owner selects draft
    NeedsApproval --> Approved: Exact recipient and content approved
    NeedsApproval --> Draft: Edit requested
    Approved --> NeedsApproval: Recipient or content changes
    Approved --> Held: Pause or suppression blocks action
    Approved --> Sending: Sending enabled and final checks pass
    Sending --> Sent: Provider confirms success
    Sending --> Failed: Confirmed failure
    Failed --> NeedsApproval: Owner reviews retry
    Sent --> Replied: Response recorded
    Sent --> Suppressed: Opt-out or stop event
    Held --> NeedsApproval: Owner reconsiders
    note right of Draft
        Planned workflow.
        Live sending and follow-ups
        are disabled in the foundation.
    end note
```

## Data contracts at a public level

| Artifact | Contains | Consumed by |
| --- | --- | --- |
| Candidate | Business identity, canonical domain and provenance | Eligibility and deduplication |
| Evidence | Observed value, source, page, time, confidence and context | Audits, scoring, reports and drafts |
| Score snapshot | Dimension values, weights, version and explanation | Ranking and owner review |
| Private report | Selected verified findings, coverage and recommendations | Prospect review after owner selection |
| Approval | Exact recipient/content, owner action and validity state | Final action gate |
| Usage record | Reserved/actual provider cost and job context | Budget enforcement and review |
| CRM event | Observed stage, response, proposal or deal outcome | Attribution and later calibration |

## Trust boundaries

The public showcase contains project explanations. The private workspace contains leads and approvals. Provider access is server-authorized and budgeted. External website text is research input, not operational instruction. Prospect reports remain private and revocable. These controls require implementation and verification before production use.
