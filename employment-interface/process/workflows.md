# Employment-SP Process Standards: Workflows

This document provides detailed workflow diagrams for the core Employment-Social Protection interoperability processes, including BPMN process flows and sequence diagrams for system interactions.

## Overview

The workflows illustrate the step-by-step processes, decision points, and system interactions for each of the five core employment use cases defined in `use-cases.md`. These diagrams support implementation planning and system integration design.

---

## PRS.EMPL.01  Beneficiary Referral to PES

### BPMN Process Flow

```mermaid
flowchart TD
    A[SP Assessment: Beneficiary Work-Able] --> B{Referral Criteria Met?}
    B -->|No| C[Update Assessment Record]
    B -->|Yes| D[Check Legal Basis]
    D --> E{Consent/Agreement Valid?}
    E -->|No| F[Request Consent/Update Agreement]
    E -->|Yes| G[Prepare Referral Data]
    G --> H[Generate Employment Referral]
    H --> I[Encrypt & Transmit to PES]
    I --> J[PES: Receive & Validate]
    J --> K{Validation Successful?}
    K -->|No| L[Return Error Response]
    K -->|Yes| M[Assign Case Worker]
    M --> N[Schedule Initial Contact]
    N --> O[Send Acknowledgment to SP]
    O --> P[Update Referral Status]
    P --> Q[Contact Beneficiary within 5 days]
    Q --> R[Begin PES Services]

    C --> S[End: No Referral]
    F --> T[End: Consent Pending]
    L --> U[End: Referral Failed]
    R --> V[End: Referral Active]

    style A fill:#e1f5fe
    style R fill:#c8e6c9
    style L fill:#ffcdd2
```

### Sequence Diagram

```mermaid
sequenceDiagram
    participant SP as SP-MIS
    participant PES as PES System
    participant CW as Case Worker
    participant B as Beneficiary

    Note over SP: Work capacity assessment completed
    SP->>SP: Check referral criteria
    SP->>SP: Validate legal basis
    SP->>SP: Prepare DO.EMPL.01 Employment Referral

    SP->>PES: POST /referrals (encrypted)
    Note over PES: Authentication & authorization
    PES->>PES: Validate referral data
    PES->>PES: Assign case worker
    PES->>SP: 201 Created {referral_id, status: PENDING}

    PES->>CW: Notify new referral assignment
    CW->>CW: Review case details
    CW->>B: Schedule initial contact (d5 days)
    CW->>PES: Update contact scheduled

    PES->>SP: POST /notifications {status: CONTACT_SCHEDULED}

    Note over CW,B: Initial PES consultation
    CW->>PES: Update status: ACTIVE
    PES->>SP: POST /notifications {status: SERVICES_COMMENCED}
```

---

## PRS.EMPL.02  Employment Status Verification

### BPMN Process Flow

```mermaid
flowchart TD
    A[SP Trigger: Verification Needed] --> B[Prepare Verification Request]
    B --> C[Authenticate with PES]
    C --> D[Send Status Query]
    D --> E[PES: Receive Request]
    E --> F[Identity Resolution]
    F --> G{Person Found?}
    G -->|No| H[Return No Match Response]
    G -->|Yes| I[Query Employment Sources]
    I --> J[Tax Authority Data]
    I --> K[Employer Records]
    I --> L[PES Internal Records]
    J --> M[Aggregate Data]
    K --> M
    L --> M
    M --> N[Apply Confidence Scoring]
    N --> O[Generate Verification Response]
    O --> P[Return to SP-MIS]
    P --> Q[SP: Process Response]
    Q --> R{Status Changed?}
    R -->|No| S[Log Verification]
    R -->|Yes| T[Flag for Review]
    T --> U[Compliance Assessment]
    U --> V{Action Required?}
    V -->|No| W[Update Record]
    V -->|Yes| X[Generate Alert]

    H --> Y[End: No Match]
    S --> Z[End: No Change]
    W --> AA[End: Updated]
    X --> BB[End: Alert Generated]

    style A fill:#e1f5fe
    style AA fill:#c8e6c9
    style BB fill:#fff3e0
    style Y fill:#ffcdd2
```

### Sequence Diagram

```mermaid
sequenceDiagram
    participant SP as SP-MIS
    participant PES as PES System
    participant TAX as Tax Authority
    participant EMP as Employer Systems

    Note over SP: Periodic compliance check triggered
    SP->>PES: GET /employment-status/{person_id}
    Note over PES: OAuth2 authentication

    PES->>PES: Resolve person identity

    par Parallel Data Gathering
        PES->>TAX: Query employment records
        TAX-->>PES: Employment history data
    and
        PES->>EMP: Query current employment
        EMP-->>PES: Current job details
    and
        PES->>PES: Check internal records
    end

    PES->>PES: Aggregate & score confidence
    PES->>PES: Generate DO.EMPL.02 Verification
    PES->>SP: 200 OK {employment_status, confidence, sources}

    SP->>SP: Compare with benefit records
    alt Status discrepancy detected
        SP->>SP: Flag for manual review
        SP->>SP: Generate compliance alert
    else Status consistent
        SP->>SP: Log successful verification
    end
```

---

## PRS.EMPL.03  Training Benefit Enrollment

### BPMN Process Flow

```mermaid
flowchart TD
    A[Career Counselor: Identify Training Need] --> B[Skills Assessment]
    B --> C[Match Training Programs]
    C --> D[Check Program Capacity]
    D --> E{Space Available?}
    E -->|No| F[Waitlist or Alternative]
    E -->|Yes| G[Calculate Benefit Impact]
    G --> H[Prepare Training Benefit Request]
    H --> I[Submit to SP-MIS]
    I --> J[SP: Validate Request]
    J --> K{Benefit Rules Allow?}
    K -->|No| L[Reject with Reason]
    K -->|Yes| M[Approve Training Arrangement]
    M --> N[Modify Benefit Schedule]
    N --> O[Notify PES: Approved]
    O --> P[Register in Training Program]
    P --> Q[Begin Training]
    Q --> R[Monitor Progress]
    R --> S{Training Completed?}
    S -->|No| T[Continue Monitoring]
    S -->|Yes| U[Issue Certificate]
    U --> V[Update Skills Profile]
    V --> W[Trigger Job Placement Process]

    F --> X[End: Deferred]
    L --> Y[End: Not Eligible]
    T --> R
    W --> Z[End: Ready for Employment]

    style A fill:#e1f5fe
    style W fill:#c8e6c9
    style L fill:#ffcdd2
```

### Sequence Diagram

```mermaid
sequenceDiagram
    participant CC as Career Counselor
    participant PES as PES Training System
    participant SP as SP-MIS
    participant TP as Training Provider
    participant B as Beneficiary

    CC->>PES: Complete skills assessment
    PES->>PES: Match to available programs
    PES->>TP: Check program capacity
    TP-->>PES: Confirm availability

    PES->>SP: POST /employment-benefits (training request)
    SP->>SP: Validate benefit continuation rules
    SP->>SP: Calculate payment adjustments
    SP->>PES: 201 Created {benefit_id, status: APPROVED}

    PES->>TP: Enroll beneficiary
    TP->>B: Notify training start date
    TP->>PES: Confirm enrollment

    Note over TP,B: Training period
    TP->>PES: Weekly progress updates
    PES->>SP: POST /notifications {progress_status}

    TP->>PES: Training completed
    TP->>B: Issue completion certificate
    PES->>PES: Update skills profile
    PES->>SP: POST /notifications {status: COMPLETED}
```

---

## PRS.EMPL.04  Job Placement Tracking

### BPMN Process Flow

```mermaid
flowchart TD
    A[PES: Job Opportunity Identified] --> B[Match Beneficiary Profile]
    B --> C[Employment Counselor Review]
    C --> D[Support Application Process]
    D --> E[Employer Decision]
    E --> F{Job Offer Made?}
    F -->|No| G[Update Search Strategy]
    F -->|Yes| H[Confirm Job Details]
    H --> I[Create Job Placement Record]
    I --> J[Notify SP-MIS]
    J --> K[SP: Calculate Benefit Transition]
    K --> L[Begin Graduated Reduction]
    L --> M[Monitor Employment Stability]
    M --> N[30-Day Check]
    N --> O[60-Day Check]
    O --> P[90-Day Confirmation]
    P --> Q{Employment Stable?}
    Q -->|No| R[Reactivate Benefits]
    Q -->|Yes| S[Placement Success Confirmed]
    S --> T[Trigger Graduation Process]

    G --> U[Continue Job Search]
    R --> V[End: Placement Failed]
    T --> W[End: Successful Placement]
    U --> B

    style A fill:#e1f5fe
    style T fill:#c8e6c9
    style R fill:#fff3e0
```

### Sequence Diagram

```mermaid
sequenceDiagram
    participant PES as PES Placement System
    participant EC as Employment Counselor
    participant EMP as Employer
    participant SP as SP-MIS
    participant B as Beneficiary

    PES->>EC: Job opportunity matched
    EC->>B: Present job opportunity
    B->>EC: Express interest
    EC->>EMP: Submit application

    EMP->>EMP: Interview process
    EMP->>EC: Job offer confirmed
    EC->>PES: POST /job-placements

    PES->>SP: POST /notifications {placement_confirmed}
    SP->>SP: Calculate benefit graduation
    SP->>B: Notify benefit transition plan

    Note over EMP,B: Employment begins

    loop Stability Monitoring
        PES->>EMP: Verify continued employment
        EMP-->>PES: Employment status confirmed
        PES->>SP: POST /notifications {employment_stable}
    end

    alt 90-day stability confirmed
        PES->>SP: POST /notifications {stable_employment}
        SP->>SP: Initiate graduation process
    else Employment terminated
        PES->>SP: POST /notifications {employment_ended}
        SP->>SP: Reactivate benefits
    end
```

---

## PRS.EMPL.05  Benefit Graduation Process

### BPMN Process Flow

```mermaid
flowchart TD
    A[Stable Employment Confirmed] --> B[Joint SP-PES Case Review]
    B --> C[Calculate Graduation Timeline]
    C --> D[Create Transition Plan]
    D --> E[Notify Beneficiary]
    E --> F[Begin Graduated Reduction]
    F --> G[Month 1: 75% Benefits]
    G --> H[Month 2: 50% Benefits]
    H --> I[Month 3: 25% Benefits]
    I --> J[Month 4: 0% Benefits]
    J --> K[Monitor Employment Status]
    K --> L[3-Month Post-Graduation Check]
    L --> M{Still Employed?}
    M -->|Yes| N[Graduation Successful]
    M -->|No| O[Employment Lost]
    O --> P{Within Grace Period?}
    P -->|Yes| Q[Reactivate Emergency Support]
    P -->|No| R[Fast-Track Re-application]
    Q --> S[Continue Monitoring]
    R --> T[Full Re-assessment Required]

    N --> U[End: Graduated Successfully]
    S --> V[End: Emergency Support Active]
    T --> W[End: Re-entering System]

    style A fill:#e1f5fe
    style N fill:#c8e6c9
    style O fill:#fff3e0
    style T fill:#ffcdd2
```

### Sequence Diagram

```mermaid
sequenceDiagram
    participant SP as SP-MIS
    participant PES as PES Follow-up
    participant SW as Social Worker
    participant B as Beneficiary
    participant EMP as Employer

    Note over SP,PES: 90-day employment stability confirmed
    SP->>PES: Initiate graduation review
    SP->>SW: Schedule joint case review

    SW->>B: Discuss graduation plan
    B->>SW: Confirm understanding
    SW->>SP: Graduation plan approved

    SP->>B: Notify benefit reduction schedule

    loop 4-month graduation period
        SP->>SP: Reduce benefits per schedule
        SP->>B: Notify monthly payment
        PES->>EMP: Verify continued employment
        EMP-->>PES: Employment confirmed
        PES->>SP: POST /notifications {employment_ongoing}
    end

    SP->>SP: Benefits ended
    SP->>B: Graduation completed notification

    Note over SP,PES: Post-graduation monitoring (3 months)
    PES->>EMP: Quarterly employment check

    alt Employment continues
        EMP-->>PES: Still employed
        PES->>SP: POST /notifications {graduation_successful}
    else Employment lost
        EMP-->>PES: Employment terminated
        PES->>SP: POST /notifications {employment_lost}
        SP->>B: Emergency support available
    end
```

---

## Cross-Process Workflow Integration

### Master Process Flow

```mermaid
flowchart TD
    A[SP Beneficiary Assessment] --> B[PRS.EMPL.01: Referral]
    B --> C[PES Services Begin]
    C --> D{Training Needed?}
    D -->|Yes| E[PRS.EMPL.03: Training Enrollment]
    D -->|No| F[Direct Job Search]
    E --> G[Training Completion]
    G --> F
    F --> H[PRS.EMPL.04: Job Placement]
    H --> I{Placement Successful?}
    I -->|No| J[Continue PES Services]
    I -->|Yes| K[Employment Stability Period]
    K --> L[PRS.EMPL.05: Benefit Graduation]

    subgraph "Ongoing Throughout"
        M[PRS.EMPL.02: Status Verification]
    end

    J --> C
    L --> N[End: Successfully Graduated]

    M --> O{Status Issues?}
    O -->|Yes| P[Compliance Review]
    O -->|No| Q[Continue Normal Process]
    P --> R[Corrective Action]
    R --> Q
    Q --> M

    style A fill:#e1f5fe
    style N fill:#c8e6c9
    style M fill:#f3e5f5
```

### Data Flow Architecture

```mermaid
graph TB
    subgraph "SP Systems"
        SP1[SP-MIS]
        SP2[Social Registry]
        SP3[Benefit Payment System]
    end

    subgraph "PES Systems"
        PES1[PES Core System]
        PES2[Training Management]
        PES3[Job Placement System]
    end

    subgraph "External Systems"
        EXT1[Tax Authority]
        EXT2[Employer Systems]
        EXT3[Training Providers]
    end

    subgraph "Data Objects"
        DO1[DO.EMPL.01: Referral]
        DO2[DO.EMPL.02: Status Verification]
        DO3[DO.EMPL.03: Training Benefit]
        DO4[DO.EMPL.04: Job Placement]
        DO5[DO.EMPL.05: Compliance Monitoring]
    end

    SP1 -->|"DO.EMPL.01: Referral"| DO1
    DO1 --> PES1

    SP1 -->|"Status Request"| DO2
    DO2 --> PES1
    PES1 -->|"DO.EMPL.02: Verification"| DO2
    DO2 -->|"Status Response"| SP1

    PES2 -->|"DO.EMPL.03: Training Benefit"| DO3
    DO3 -->|"Benefit Request"| SP1

    PES3 -->|"DO.EMPL.04: Job Placement"| DO4
    DO4 -->|"Placement Notification"| SP1

    SP1 -->|"DO.EMPL.05: Compliance Query"| DO5
    DO5 --> PES1

    EXT1 -->|"Employment Records"| DO2
    EXT2 -->|"Job Status"| DO2
    EXT2 -->|"Placement Confirmation"| DO4
    EXT3 -->|"Training Progress"| DO3

    style DO1 fill:#e3f2fd
    style DO2 fill:#e8f5e8
    style DO3 fill:#fff3e0
    style DO4 fill:#f3e5f5
    style DO5 fill:#ffebee
```

---

## DCI Ecosystem Integration Workflows

### Cross-Interface Coordination Flow

```mermaid
sequenceDiagram
    participant SR as Social Registry
    participant EMPL as Employment-SP
    participant IBR as IBR
    participant PAY as Payment System
    participant CRVS as CRVS
    participant GIP as Gov Interop Platform

    Note over SR: Work capacity assessment completed
    SR->>GIP: POST /notify (work_capacity_assessed)
    GIP->>EMPL: Forward notification

    EMPL->>CRVS: GET /person-verification
    Note over CRVS: DCI authentication & identity resolution
    CRVS-->>EMPL: Person identity confirmed

    EMPL->>IBR: GET /person/{id}/benefits
    Note over IBR: Check current benefit enrollment
    IBR-->>EMPL: Current benefit status

    EMPL->>EMPL: Process employment referral
    EMPL->>IBR: PUT /person/{id}/status (benefit_coordination)
    EMPL->>PAY: POST /subsidies (employment_support)

    EMPL->>GIP: POST /notify (employment_services_commenced)
    GIP->>SR: Forward status update
    GIP->>IBR: Forward coordination update
```

### DCI Authentication Flow

```mermaid
sequenceDiagram
    participant CLIENT as SP-MIS Client
    participant AUTH as DCI Auth Service
    participant EMPL as Employment-SP API
    participant GIP as Gov Interop Platform

    CLIENT->>AUTH: POST /oauth2/token (client_credentials)
    Note over AUTH: DCI-compliant JWT generation
    AUTH-->>CLIENT: JWT with DCI claims

    CLIENT->>CLIENT: Prepare DCI message structure
    Note over CLIENT: signature + header + message format

    CLIENT->>GIP: POST /employment/referrals (DCI message)
    GIP->>GIP: Validate signature & route
    GIP->>EMPL: Forward to Employment-SP interface

    EMPL->>EMPL: Process employment referral
    EMPL->>GIP: DCI-compliant response
    GIP->>CLIENT: Forward response
```

### Government Interoperability Platform Integration

```mermaid
graph TB
    subgraph "DCI Ecosystem"
        SR[Social Registry]
        IBR[Integrated Beneficiary Registry]
        CRVS[Civil Registration]
        PAY[Payment Systems]
        DR[Disability Registry]
    end

    subgraph "Government Interoperability Platform"
        AUTH[Authentication Service]
        ROUTE[Message Routing]
        AUDIT[Audit & Logging]
        TRANS[Data Transformation]
    end

    subgraph "Employment-SP Interface"
        API[Employment API]
        PROC[Process Engine]
        DATA[Data Store]
    end

    SR -->|Work Capacity| ROUTE
    ROUTE -->|Referral| API
    API -->|Status Updates| ROUTE
    ROUTE -->|Coordination| IBR

    API -->|Identity Verification| AUTH
    AUTH -->|Verified Identity| CRVS

    API -->|Payment Requests| TRANS
    TRANS -->|Transformed| PAY

    API -->|Audit Events| AUDIT
    PROC -->|Business Events| AUDIT

    style AUTH fill:#e3f2fd
    style ROUTE fill:#e8f5e8
    style AUDIT fill:#fff3e0
    style API fill:#f3e5f5
```

---

## Implementation Considerations

### Performance Requirements
- **Referral Processing**: <5 seconds end-to-end
- **Status Verification**: <2 seconds response time
- **Batch Operations**: Support for 1000+ records/hour
- **Concurrent Users**: 100+ simultaneous system connections

### Error Handling Patterns
- **Retry Logic**: Exponential backoff for temporary failures
- **Circuit Breaker**: Prevent cascade failures during outages
- **Dead Letter Queue**: Handle unprocessable messages
- **Compensation Transactions**: Rollback for partial failures

### Security Considerations
- **End-to-End Encryption**: All sensitive data in transit
- **Audit Logging**: Complete activity trail for compliance
- **Access Control**: Role-based permissions at API level
- **Data Minimization**: Only necessary data in each exchange

### Monitoring and Alerting
- **SLA Monitoring**: Track response times and availability
- **Business Metrics**: Placement rates, graduation success
- **Error Rate Tracking**: Identify system integration issues
- **Compliance Reporting**: Regular audit and performance reports

---

### DCI Integration Requirements
- **Cross-Interface Coordination**: All workflows validated against DCI ecosystem patterns
- **Message Structure Compliance**: Signature + header + message format verified for all API calls
- **Identity Resolution**: Person identification consistent across all DCI interfaces
- **Government Platform Integration**: Workflows tested with both direct and platform-mediated patterns

---

## Quality Assurance Checklist

### DCI Compliance Validation
- [ ] All workflows include DCI-standard authentication patterns
- [ ] Cross-interface coordination properly sequenced
- [ ] JSON-LD message structures validated against DCI schemas
- [ ] Event-driven notifications aligned with DCI ecosystem requirements

### Performance & SLA Requirements
- [ ] Cross-interface response times: < 5 seconds end-to-end
- [ ] DCI message routing latency: < 1 second platform overhead
- [ ] Government Interoperability Platform integration tested
- [ ] Fallback patterns validated for platform unavailability

### Security & Audit Compliance
- [ ] End-to-end encryption for all cross-interface data exchange
- [ ] DCI-compliant audit logging for all transactions
- [ ] Cross-border data transfer compliance where applicable
- [ ] Role-based access control consistent across DCI ecosystem

---

## Open Issues

`TODO(technical)`: Validate sequence diagrams with actual DCI API specifications from dci-standards repository
`TODO(performance)`: Define specific SLA requirements for Government Interoperability Platform integration
`TODO(security)`: Detail DCI-compliant encryption and digital signature requirements
`TODO(governance)`: Align workflow error handling with DCI ecosystem resilience patterns