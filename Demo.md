# Functional Specification Document

**Project:** DCI Employment-SP Interoperability Demo (Two-System Simulation)
**Author:** Thomas Byrnes, Digital Social Protection Expert
**Organization:** Digital Convergence Initiative (DCI) / GIZ
**Date:** September 23, 2025
**Version:** 4.0 (Enhanced Technical Specification)

## Version Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 22 Sept 2025 | Thomas Byrnes | Initial draft for a single-application demo |
| 2.0 | 22 Sept 2025 | Thomas Byrnes | Revised to a two-application simulation. Added API log panel |
| 3.0 | 23 Sept 2025 | Thomas Byrnes | Added details on startup, error handling, and extensibility |
| 4.0 | 23 Sept 2025 | Thomas Byrnes | Complete restructure with comprehensive technical specifications |

---
## 1. Introduction

### 1.1 Purpose of this Document
This Functional Specification Document (FSD) defines the comprehensive requirements for a demonstration environment consisting of two separate web applications: a mock Social Protection (SP) System and a mock Public Employment Service (PES) System. This document serves as the definitive technical blueprint for the SolDevelo development team and establishes the acceptance criteria for project delivery.

### 1.2 Project Background & Strategic Goal
The primary objective is to create a tangible, interactive demonstration of the DCI's bidirectional interoperability standards. By simulating two distinct systems that query and update each other in real-time, this demo will:

- **Illustrate Real-World Integration**: Demonstrate how SP and PES systems can seamlessly exchange data
- **Showcase Business Value**: Prove how interoperability improves service delivery and creates pathways out of poverty
- **Validate Technical Standards**: Test DCI Employment-SP API patterns in a controlled environment
- **Enable Stakeholder Engagement**: Provide a compelling visual demonstration for policymakers and technical teams

### 1.3 Scope Definition

#### 1.3.1 In Scope
- **Two Distinct Web Applications**: Independent SP System and PES System with separate codebases
- **Bidirectional API Communication**: Real-time data exchange between both systems
- **Core Use Cases**: Employment status verification and benefit eligibility checks
- **Mock Data Management**: In-memory data stores for demonstration purposes
- **Real-Time API Logging**: Visual display of all API interactions
- **Error Handling**: Comprehensive error scenarios and recovery mechanisms
- **Responsive UI Design**: Professional interfaces optimized for demonstration

#### 1.3.2 Out of Scope
- **Production Deployment**: This is a demonstration environment only
- **Persistent Data Storage**: No database integration required
- **User Authentication**: No login or security features needed
- **Performance Optimization**: Focus on functionality over scalability
- **Mobile Optimization**: Desktop browser demonstration only

#### 1.3.3 Future Extensibility
The architecture must support:
- **Modular Design**: Easy addition of new use cases
- **API Versioning**: Support for future DCI standard updates
- **Configuration Management**: Flexible system configuration options
- **Logging Enhancement**: Expandable logging and monitoring capabilities

## 2. System Architecture & Technical Overview

### 2.1 Overall Architecture
The demonstration environment consists of two independent web applications that communicate via RESTful APIs:

```
┌─────────────────────┐    HTTP/REST APIs    ┌─────────────────────┐
│   SP System         │◄────────────────────►│   PES System        │
│   (localhost:3000)  │                      │   (localhost:3001)  │
│                     │                      │                     │
│ ┌─────────────────┐ │                      │ ┌─────────────────┐ │
│ │ Frontend (React)│ │                      │ │ Frontend (React)│ │
│ │ - Dashboard UI  │ │                      │ │ - Dashboard UI  │ │
│ │ - API Log Panel │ │                      │ │ - API Log Panel │ │
│ └─────────────────┘ │                      │ └─────────────────┘ │
│ ┌─────────────────┐ │                      │ ┌─────────────────┐ │
│ │ Backend (Node)  │ │                      │ │ Backend (Node)  │ │
│ │ - REST APIs     │ │                      │ │ - REST APIs     │ │
│ │ - Mock Data     │ │                      │ │ - Mock Data     │ │
│ │ - HTTP Client   │ │                      │ │ - HTTP Client   │ │
│ └─────────────────┘ │                      │ └─────────────────┘ │
└─────────────────────┘                      └─────────────────────┘
```

### 2.2 Technology Stack
- **Frontend**: React.js with TypeScript
- **Backend**: Node.js with Express.js
- **HTTP Client**: Axios for inter-system communication
- **Styling**: CSS modules or Styled Components
- **Build Tool**: Vite or Create React App
- **Package Manager**: npm

### 2.3 Port Configuration
- **SP System**: `http://localhost:3000`
- **PES System**: `http://localhost:3001`
- **Startup Command**: `npm run start:demo` (launches both applications concurrently)

### 2.4 Data Storage Strategy
- **In-Memory Storage**: All data stored in application memory
- **Mock Data Sets**: Pre-populated sample beneficiaries and job seekers
- **State Management**: React state with Context API or Redux Toolkit
- **Data Persistence**: Session-based only (resets on application restart)

## 3. Data Models & API Specifications

### 3.1 Standards Compliance Overview

This demo implements simplified versions of the **DCI Employment-SP Interoperability Standards** defined in `/employment-interface/`. The data models and API patterns are based on:

- **PRS.EMPL.02**: Status Verification Process
- **DO.EMPL.01**: Employment Referral data object
- **API.EMPL.02**: Employment status verification endpoint
- **CD.EMPL.01**: Employment status enumerations

### 3.2 Enhanced Data Models (Standards-Aligned)

#### 3.2.1 SP System Data Model: Beneficiary
```typescript
interface SPBeneficiary {
  // Core Identification (DO.COM.01 Person)
  id: string;                    // Unique identifier (e.g., "BEN123")
  firstName: string;             // Given name
  lastName: string;              // Surname
  nationalId: string;            // National identification number

  // SP Benefit Information (DO.EMPL.01 Extensions)
  benefitStatus: 'active' | 'suspended' | 'under_review' | 'terminated';
  benefitType: 'cash_transfer' | 'food_assistance' | 'housing_support' | 'unemployment_insurance';
  enrollmentDate: string;        // ISO 8601 date format
  lastStatusUpdate: string;      // ISO 8601 timestamp
  sourceProgram: {               // Following DO.EMPL.01 structure
    programIdentifier: string;   // e.g., "SP-CASH-2024"
    programName: string;         // e.g., "Family Emergency Income"
    implementingInstitution: string;
  };

  // Employment Information (CD.EMPL.01 EmploymentStatus)
  employmentStatus?: 'employed' | 'not_employed' | 'seeking_work' | 'underemployed' |
                     'unable_to_work' | 'self_employed' | 'unknown';
  lastEmploymentCheck?: string;  // ISO 8601 timestamp of last PES query

  // Referral Information (CD.EMPL.02 ReferralReason)
  referralReason?: 'unemployment_benefit_activation' | 'conditionality_enforcement' |
                   'work_ability_assessment' | 'skills_mismatch';
  referralStatus?: 'pending' | 'accepted' | 'rejected' | 'completed';

  // Work Capacity Assessment (DO.EMPL.01 Extensions)
  workCapacityAssessment?: {
    assessmentDate: string;
    workAble: boolean;
    restrictions?: string[];     // e.g., ["part_time_only"]
    skills?: string[];          // e.g., ["customer_service", "basic_literacy"]
    workPreferences?: {
      preferredSectors?: string[];
      geographicMobility?: 'local_only' | 'regional' | 'national';
      availability?: 'immediate' | 'within_month' | 'within_quarter';
    };
  };
}
```

#### 3.2.2 PES System Data Model: JobSeeker
```typescript
interface PESJobSeeker {
  // Core Identification (DO.COM.01 Person)
  id: string;                    // Unique identifier (e.g., "JS456")
  firstName: string;             // Given name
  lastName: string;              // Surname
  nationalId: string;            // National identification number

  // Employment Information (CD.EMPL.01 EmploymentStatus)
  employmentStatus: 'employed' | 'not_employed' | 'seeking_work' | 'underemployed' |
                    'temporary_layoff' | 'self_employed' | 'unable_to_work';
  registrationDate: string;      // ISO 8601 date format
  lastStatusUpdate: string;      // ISO 8601 timestamp

  // Skills and Capacity (DO.EMPL.01 Extensions)
  skillsProfile: string[];       // Array of skills/competencies
  educationLevel?: 'primary' | 'secondary' | 'tertiary' | 'vocational';
  workExperience?: {
    yearsExperience: number;
    lastEmploymentDate?: string;
    previousSectors?: string[];
  };

  // SP Benefit Information (Updated from SP System)
  spBenefitStatus?: 'active' | 'suspended' | 'under_review' | 'terminated' | 'unknown';
  spBenefitType?: string;        // Updated from SP query
  lastBenefitCheck?: string;     // ISO 8601 timestamp of last SP query

  // Training and Services (PRS.EMPL.03 Training Benefits)
  trainingEligibility?: 'eligible' | 'ineligible' | 'conditional' | 'pending';
  eligibilityConditions?: string[]; // Conditions if conditional eligibility
  eligibilityScore?: number;     // 0.0-1.0 vulnerability/eligibility score

  // Service History
  pesServices?: {
    servicesReceived: string[];  // e.g., ["job_counseling", "skills_training"]
    lastServiceDate?: string;
    caseWorkerAssigned?: string;
  };
}
```

### 3.3 API Response Models (DCI-Aligned)

#### 3.3.1 Employment Status Response (PES → SP)
Based on **PRS.EMPL.02 Status Verification Process**:

```typescript
interface EmploymentStatusResponse {
  // Core Response (following DCI patterns)
  beneficiaryId: string;
  employmentStatus: 'employed' | 'not_employed' | 'seeking_work' | 'underemployed' |
                    'temporary_layoff' | 'self_employed' | 'unable_to_work';
  lastUpdate: string;            // ISO 8601 timestamp

  // Employment Details (DO.EMPL.02 Employment Status Verification)
  employmentDetails?: {
    employer?: string;
    position?: string;
    startDate?: string;
    employmentType?: 'paid_employee' | 'self_employed' | 'own_account_worker' |
                     'cooperative_member' | 'family_worker';
    workingHours?: 'full_time' | 'part_time' | 'irregular';
    sector?: string;             // Economic sector
    monthlyIncome?: {
      amount: number;
      currency: string;          // ISO 4217 currency code
    };
  };

  // Verification Information (PRS.EMPL.02 Evidence Requirements)
  verificationDetails: {
    confidence: number;          // 0.0-1.0 confidence score
    verificationSource: string;  // e.g., "payroll_system", "tax_authority"
    verificationMethod: 'real_time' | 'batch' | 'manual';
    lastVerificationDate: string;
    sourceSystemId: string;
  };

  // SP Benefit Impact (CD.EMPL.01 SP Benefit Impact)
  spBenefitImpact: 'benefit_graduation_review' | 'benefit_continuation' |
                   'benefit_suspension' | 'income_verification_required';
}
```

#### 3.3.2 Benefit Status Response (SP → PES)
Based on **DO.EMPL.01 Employment Referral** and **PRS.EMPL.03 Training Benefits**:

```typescript
interface BenefitStatusResponse {
  // Core Response
  jobSeekerId: string;
  benefitStatus: 'active' | 'suspended' | 'under_review' | 'terminated';
  benefitType: 'cash_transfer' | 'food_assistance' | 'housing_support' | 'unemployment_insurance';
  enrollmentDate: string;
  lastUpdate: string;            // ISO 8601 timestamp

  // Program Information (DO.EMPL.01 Source Programme)
  sourceProgram: {
    programIdentifier: string;   // e.g., "SP-CASH-2024"
    programName: string;         // e.g., "Family Emergency Income"
    implementingInstitution: string;
    programType: 'conditional' | 'unconditional' | 'categorical';
  };

  // Eligibility and Targeting (RSH-style vulnerability scoring)
  eligibilityDetails: {
    eligibilityScore: number;    // 0.0-1.0 vulnerability/eligibility score
    vulnerabilityPercentile?: number; // 0-100 percentile ranking
    targetingCriteria: string[]; // e.g., ["income_below_poverty", "household_with_children"]
    meansTested: boolean;
  };

  // Training Eligibility (PRS.EMPL.03 Training Benefits)
  trainingEligibility: 'eligible' | 'ineligible' | 'conditional' | 'pending';
  eligibilityConditions?: string[]; // Conditions if conditional eligibility
  subsidyEligibility?: {
    trainingSubsidy: boolean;
    transportAllowance: boolean;
    childcareSupport: boolean;
    maxSubsidyAmount?: {
      amount: number;
      currency: string;
    };
  };

  // Work Capacity (DO.EMPL.01 Work Capacity Assessment)
  workCapacity?: {
    workAble: boolean;
    restrictions?: string[];     // e.g., ["part_time_only", "no_heavy_lifting"]
    skills?: string[];          // Skills assessment
    lastAssessmentDate?: string;
  };
}
```

### 3.3 API Endpoints Specification

#### 3.3.1 SP System API Endpoints

**GET /api/v1/benefit-status/{beneficiaryId}**
- **Purpose**: Retrieve benefit status for a specific beneficiary
- **Parameters**:
  - `beneficiaryId` (path): Unique beneficiary identifier
- **Response**: `BenefitStatusResponse`
- **Status Codes**: 200 (success), 404 (not found), 500 (server error)

**GET /api/v1/beneficiaries**
- **Purpose**: Retrieve all beneficiaries for dashboard display
- **Response**: `SPBeneficiary[]`

**PUT /api/v1/beneficiaries/{beneficiaryId}/employment-status**
- **Purpose**: Update employment status from PES data
- **Request Body**: `EmploymentStatusResponse`
- **Response**: Updated `SPBeneficiary`

#### 3.3.2 PES System API Endpoints

**GET /api/v1/employment-status/{beneficiaryId}**
- **Purpose**: Retrieve employment status for a specific individual
- **Parameters**:
  - `beneficiaryId` (path): Unique individual identifier
- **Response**: `EmploymentStatusResponse`
- **Status Codes**: 200 (success), 404 (not found), 500 (server error)

**GET /api/v1/job-seekers**
- **Purpose**: Retrieve all job seekers for dashboard display
- **Response**: `PESJobSeeker[]`

**PUT /api/v1/job-seekers/{jobSeekerId}/benefit-status**
- **Purpose**: Update benefit status from SP data
- **Request Body**: `BenefitStatusResponse`
- **Response**: Updated `PESJobSeeker`

## 4. Functional Requirements & Use Cases

### 4.1 SP System: Employment Status Verification

#### 4.1.1 Use Case Overview
**Actor**: SP Case Worker
**Goal**: Verify employment status of beneficiaries to update benefit eligibility
**Trigger**: Manual verification request or scheduled review

#### 4.1.2 User Story
"As an SP case worker, I need to check if a beneficiary has found employment by querying the PES system, so I can update their benefit status and initiate appropriate reviews or adjustments."

#### 4.1.3 Functional Requirements

**FR-SP-001**: **Beneficiary Dashboard Display**
- Display a table of all SP beneficiaries with key information
- Include columns: ID, Name, Benefit Type, Status, Employment Status, Last Updated
- Show "Unknown" for employment status until verified with PES

**FR-SP-002**: **Employment Status Check Action**
- Provide "Check Employment Status" button for each beneficiary
- Button should be disabled during API call and show loading state
- Update button text to "Checking..." during operation

**FR-SP-003**: **Real-Time Status Updates**
- Update beneficiary employment status immediately upon PES response
- Change benefit status to "Under Review" if employment is detected
- Refresh "Last Updated" timestamp automatically

**FR-SP-004**: **API Logging Panel**
- Display real-time log of all API interactions at bottom of screen
- Show timestamp, request method, URL, and response status
- Use color coding: green for success, red for errors, yellow for warnings
- Maintain scrollable history of last 50 interactions

#### 4.1.4 Acceptance Criteria

**AC-SP-001**: Dashboard Display
```gherkin
Given I am on the SP System dashboard
When the page loads
Then I should see a table with all beneficiaries
And each row should show: ID, Name, Benefit Type, Current Status, Employment Status
And employment status should initially show "Unknown" for all beneficiaries
```

**AC-SP-002**: Employment Status Check
```gherkin
Given I am viewing a beneficiary with "Unknown" employment status
When I click "Check Employment Status"
Then the system should send GET request to PES /api/v1/employment-status/{id}
And the button should show "Checking..." and be disabled
And within 5 seconds I should see the updated employment status
And the button should return to "Check Employment Status" and be enabled
```

**AC-SP-003**: Status Update Logic
```gherkin
Given PES returns employment status "employed"
When the SP system receives this response
Then the beneficiary's employment status should update to "Employed"
And the benefit status should change to "Under Review"
And the last updated timestamp should reflect current time
```

**AC-SP-004**: Error Handling
```gherkin
Given the PES system is unavailable
When I click "Check Employment Status"
Then I should see an error message in the API log panel
And the error should be displayed in red text
And the beneficiary's status should remain unchanged
And the system should not crash or become unresponsive
```

### 4.2 PES System: Benefit Status Verification

#### 4.2.1 Use Case Overview
**Actor**: PES Case Worker
**Goal**: Verify SP benefit status of job seekers for training program eligibility
**Trigger**: Training program enrollment or eligibility assessment

#### 4.2.2 User Story
"As a PES case worker, I need to check if a job seeker is receiving SP benefits by querying the SP system, so I can determine their eligibility for subsidized training programs and prioritize services."

#### 4.2.3 Functional Requirements

**FR-PES-001**: **Job Seeker Dashboard Display**
- Display a table of all registered job seekers
- Include columns: ID, Name, Employment Status, SP Benefit Status, Training Eligibility
- Show "Unknown" for SP benefit status until verified

**FR-PES-002**: **Benefit Status Check Action**
- Provide "Check SP Benefit Status" button for each job seeker
- Button should be disabled during API call and show loading state
- Update button text to "Checking..." during operation

**FR-PES-003**: **Training Eligibility Determination**
- Automatically update training eligibility based on SP benefit status
- Set to "Eligible" if SP benefits are active
- Set to "Ineligible" if SP benefits are inactive or terminated
- Set to "Conditional" for special cases

**FR-PES-004**: **API Logging Panel**
- Mirror SP system logging functionality
- Display all SP system queries and responses
- Maintain interaction history with color-coded status indicators

#### 4.2.4 Acceptance Criteria

**AC-PES-001**: Dashboard Display
```gherkin
Given I am on the PES System dashboard
When the page loads
Then I should see a table with all job seekers
And each row should show: ID, Name, Employment Status, SP Benefits, Training Eligibility
And SP benefit status should initially show "Unknown" for all job seekers
```

**AC-PES-002**: Benefit Status Check
```gherkin
Given I am viewing a job seeker with "Unknown" SP benefit status
When I click "Check SP Benefit Status"
Then the system should send GET request to SP /api/v1/benefit-status/{id}
And the button should show "Checking..." and be disabled
And within 5 seconds I should see the updated benefit status
And training eligibility should be automatically determined
```

**AC-PES-003**: Eligibility Logic
```gherkin
Given SP returns benefit status "active"
When the PES system receives this response
Then the job seeker's SP benefit status should update to "Active"
And training eligibility should automatically change to "Eligible"
And the last updated timestamp should reflect current time
```

**AC-PES-004**: Cross-System Data Consistency
```gherkin
Given a person exists in both SP and PES systems with same nationalId
When either system queries the other
Then the response should contain consistent personal information
And the query should complete successfully
And both systems should update their local state accordingly
```

## 5. Non-Functional Requirements

### 5.1 Performance Requirements
- **Response Time**: API calls must complete within 5 seconds under normal conditions
- **Startup Time**: Both applications must start and be ready within 30 seconds
- **Concurrent Users**: Support demonstration with up to 5 concurrent browser sessions
- **Memory Usage**: Each application should use less than 512MB RAM

### 5.2 Reliability Requirements
- **Error Recovery**: Applications must not crash on API failures
- **Data Consistency**: Mock data must remain consistent across application restarts
- **Service Availability**: 99% uptime during demonstration sessions

### 5.3 Usability Requirements
- **Interface Responsiveness**: UI updates must be visible within 1 second of user action
- **Error Messages**: Clear, non-technical error messages for demonstration audience
- **Browser Compatibility**: Support Chrome, Firefox, Safari, and Edge (latest versions)
- **Screen Resolution**: Optimized for 1920x1080 resolution for presentation

### 5.4 Security Requirements (Demo Level)
- **CORS Configuration**: Properly configured for localhost cross-origin requests
- **Input Validation**: Basic validation on all API endpoints
- **Error Disclosure**: No sensitive system information in error messages
- **Secure Defaults**: All demo data should be obviously fictional

## 6. Technical Implementation Requirements

### 6.1 Development Standards
- **Code Quality**: ESLint and Prettier configuration for consistent formatting
- **Type Safety**: TypeScript for all components and API interfaces
- **Testing**: Unit tests for core API functionality (minimum 70% coverage)
- **Documentation**: Inline code comments for complex business logic

### 6.2 Deployment Architecture
```
Project Structure:
├── package.json (root - scripts for concurrent startup)
├── sp-system/
│   ├── frontend/ (React + TypeScript)
│   ├── backend/ (Node.js + Express)
│   └── package.json
├── pes-system/
│   ├── frontend/ (React + TypeScript)
│   ├── backend/ (Node.js + Express)
│   └── package.json
├── shared/
│   ├── types/ (Common TypeScript interfaces)
│   └── mock-data/ (Sample datasets)
└── docs/
    ├── demo-script.md
    └── troubleshooting.md
```

### 6.3 Mock Data Requirements (Standards-Aligned)

#### 6.3.1 SP System Sample Data
Minimum 5 beneficiaries implementing **DO.EMPL.01** structure:

```json
[
  {
    "id": "BEN001",
    "firstName": "Maria",
    "lastName": "Rodriguez",
    "nationalId": "12345678901",
    "benefitStatus": "active",
    "benefitType": "cash_transfer",
    "enrollmentDate": "2024-01-15",
    "lastStatusUpdate": "2025-09-20T10:00:00Z",
    "sourceProgram": {
      "programIdentifier": "SP-CASH-2024",
      "programName": "Family Emergency Income",
      "implementingInstitution": "Ministry of Social Development"
    },
    "employmentStatus": "unknown",
    "referralReason": "unemployment_benefit_activation",
    "workCapacityAssessment": {
      "assessmentDate": "2024-01-20",
      "workAble": true,
      "skills": ["customer_service", "basic_literacy"],
      "workPreferences": {
        "preferredSectors": ["retail", "services"],
        "geographicMobility": "local_only",
        "availability": "immediate"
      }
    }
  },
  {
    "id": "BEN002",
    "firstName": "Carlos",
    "lastName": "Silva",
    "nationalId": "98765432109",
    "benefitStatus": "under_review",
    "benefitType": "unemployment_insurance",
    "employmentStatus": "employed",
    "referralReason": "conditionality_enforcement",
    "workCapacityAssessment": {
      "workAble": true,
      "restrictions": ["part_time_only"]
    }
  }
]
```

#### 6.3.2 PES System Sample Data
Minimum 5 job seekers implementing enhanced **PESJobSeeker** model:

```json
[
  {
    "id": "JS001",
    "firstName": "Maria",
    "lastName": "Rodriguez",
    "nationalId": "12345678901",
    "employmentStatus": "seeking_work",
    "registrationDate": "2024-02-01",
    "lastStatusUpdate": "2025-09-20T10:00:00Z",
    "skillsProfile": ["customer_service", "sales", "basic_computer"],
    "educationLevel": "secondary",
    "workExperience": {
      "yearsExperience": 3,
      "lastEmploymentDate": "2023-12-31",
      "previousSectors": ["retail"]
    },
    "spBenefitStatus": "unknown",
    "trainingEligibility": "pending",
    "pesServices": {
      "servicesReceived": ["job_counseling"],
      "lastServiceDate": "2025-09-15",
      "caseWorkerAssigned": "CW001"
    }
  },
  {
    "id": "JS002",
    "firstName": "John",
    "lastName": "Smith",
    "nationalId": "55566677788",
    "employmentStatus": "not_employed",
    "spBenefitStatus": "unknown",
    "trainingEligibility": "pending",
    "skillsProfile": ["construction", "manual_labor"]
  }
]
```

#### 6.3.3 Cross-System Data Relationships
- **Maria Rodriguez** (BEN001/JS001): Exists in both systems with matching nationalId
- **John Smith** (JS002): Exists in PES, will query SP for benefit status
- **Carlos Silva** (BEN002): Exists in SP, will query PES for employment status
- Include edge cases: person not found, system unavailable, partial data matches

## 7. Testing & Quality Assurance

### 7.1 Testing Scenarios

**TS-001: Happy Path Demo Flow**
1. Start both applications successfully
2. Verify dashboard displays mock data correctly
3. Execute employment status check from SP → PES
4. Execute benefit status check from PES → SP
5. Verify API logs display correctly
6. Confirm no console errors or warnings

**TS-002: Error Handling**
1. Stop one application while other is running
2. Attempt cross-system query
3. Verify graceful error handling
4. Restart stopped application
5. Verify recovery and normal operation

**TS-003: Data Consistency**
1. Execute multiple queries between systems
2. Verify data updates persist within session
3. Restart applications
4. Verify mock data resets to initial state

### 7.2 Acceptance Criteria for Delivery
- ✅ All functional requirements (FR-SP-001 through FR-PES-004) implemented
- ✅ All acceptance criteria (AC-SP-001 through AC-PES-004) passing
- ✅ Single command startup (`npm run start:demo`) working
- ✅ Both applications accessible at specified ports
- ✅ API logging panel functioning in both systems
- ✅ Error handling prevents application crashes
- ✅ Demo script can be executed successfully
- ✅ Code properly documented and formatted
- ✅ README.md and troubleshooting documentation complete

## 8. Standards Compliance & Advanced Features

### 8.1 Demo Mode vs. Standards Mode

The application must support two operational modes to demonstrate both simplicity and standards compliance:

#### 8.1.1 Demo Mode (Default)
**Purpose**: Simplified demonstration focusing on business value
**Features**:
- Simplified REST APIs without full DCI message structure
- Basic authentication (API keys instead of OAuth2/JWT)
- Streamlined data models showing essential fields only
- Simple error messages for non-technical audience
- Focus on cross-system integration and business outcomes

#### 8.1.2 Standards Mode (Advanced)
**Purpose**: Demonstrate full DCI Employment-SP standards compliance
**Features**:
- **Full DCI Message Structure**: Three-part signature + header + encrypted message
- **Complete Data Models**: All fields from DO.EMPL.01, DO.EMPL.02, etc.
- **Standard Enumerations**: Use CD.EMPL.01 employment status codes
- **Proper Error Handling**: DCI-compliant error responses with correlation IDs
- **Realistic Headers**: X-Correlation-ID, X-Request-ID, X-API-Version

#### 8.1.3 Mode Toggle Implementation
```typescript
interface AppConfig {
  mode: 'demo' | 'standards';
  features: {
    showFullMessageStructure: boolean;
    useStandardEnumerations: boolean;
    showEncryptionPlaceholders: boolean;
    enableAdvancedLogging: boolean;
  };
}
```

**UI Toggle**: Prominent switch in both applications:
- "Demo Mode" ↔ "Standards Mode"
- Tooltip explaining the difference
- Persists choice in local storage
- Changes visible immediately without restart

### 8.2 Standards Compliance Documentation

#### 8.2.1 Implemented Standards
This demo validates the following DCI Employment-SP standards:

| Standard | Implementation Level | Demo Features |
|----------|---------------------|---------------|
| **PRS.EMPL.02** | Core use case | Status verification workflow |
| **DO.EMPL.01** | Data structure | Employment referral object |
| **DO.EMPL.02** | Data structure | Employment status verification |
| **CD.EMPL.01** | Enumerations | Employment status codes |
| **CD.EMPL.02** | Enumerations | Referral reason codes |
| **API.EMPL.02** | Endpoint pattern | Employment status endpoint |

#### 8.2.2 Standards Simplifications for Demo
**Message Structure**:
- Demo Mode: Simple JSON REST
- Standards Mode: Shows DCI three-part structure (signature placeholder)

**Authentication**:
- Demo Mode: Basic API keys
- Standards Mode: OAuth2/JWT placeholders

**Encryption**:
- Demo Mode: Plain text (localhost only)
- Standards Mode: Shows JWE encryption placeholders

**Error Handling**:
- Demo Mode: Simple HTTP status codes
- Standards Mode: DCI-compliant error objects with correlation tracking

### 8.3 Real-World Evidence Integration

The demo incorporates evidence from operational systems:

#### 8.3.1 Country Implementation References
- **Chile RSH-SENCE**: Vulnerability percentile scoring in mock data
- **South Korea Work24**: AI-enhanced matching concepts in PES system
- **Germany SGB**: Comprehensive compliance monitoring in error scenarios
- **Australia JobActive**: Outcome-based eligibility determination

#### 8.3.2 Performance Benchmarks
Mock realistic processing times based on country evidence:
- **Chile**: 18-hour average referral processing (demo: 2-second simulation)
- **South Korea**: 96% Job Diary adoption (demo: show mobile-first design)
- **Germany**: 2.3-day decision time (demo: instant with "processing simulation")

## 9. Delivery Expectations

### 9.1 Code Deliverables
- Complete source code in GitHub repository with standards alignment documentation
- Comprehensive README.md with setup instructions and standards explanation
- Demo presentation script highlighting both business value and technical standards
- Troubleshooting guide for common issues
- **Standards Mapping Document**: Clear explanation of how demo relates to employment-interface standards

### 9.2 Handover Requirements
- Live demonstration showing both Demo Mode and Standards Mode
- Code walkthrough with development team explaining standards alignment
- Documentation review and approval from Thomas Byrnes
- Knowledge transfer session covering:
  - How to extend demo with additional standards
  - Connection between demo and full DCI implementation
  - Path for transitioning demo code to production standards

### 9.3 Success Criteria
The project will be considered successfully delivered when:
1. **Demo Execution**: Thomas Byrnes can execute full presentation script in both modes without technical issues
2. **Standards Validation**: Standards Mode accurately represents DCI Employment-SP patterns
3. **Business Value**: Demo clearly demonstrates interoperability benefits to non-technical stakeholders
4. **Technical Accuracy**: All data models and API patterns align with employment-interface specifications
5. **Extensibility**: Architecture supports adding additional PRS.EMPL standards (03, 04, 05)
6. **Documentation Quality**: Clear explanation of demo's relationship to full standards implementation

---

**Document Status**: Complete Technical Specification
**Review Required**: Development Team Technical Review
**Approval Required**: Thomas Byrnes (Project Lead)
**Implementation Start**: Upon FSD approval