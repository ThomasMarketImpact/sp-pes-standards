# PRS.EMPL.02 — Status Verification Process

**Use Case**: Verify employment status and benefit eligibility of individuals across SP and PES systems

## 📊 **Evidence Base from ILO Analysis**

### **Countries with Implementation**
1. **South Africa** - SASSA-UIF employment status coordination
2. **South Korea** - KEIS-NBLSS integrated eligibility verification
3. **Chile** - RSH employment status validation for targeting
4. **Kazakhstan** - Enbek employment verification system
5. **Australia** - Centrelink employment status monitoring

### **Digital Integration Maturity**
- **High**: South Korea, Australia (real-time status verification)
- **Medium**: Chile, South Africa (periodic status checking)
- **Developing**: Kazakhstan (manual verification processes)

## 🎯 **Use Case Definition**

### **Actors**
- **Primary**: PES (Public Employment Services)
- **Secondary**: SP-MIS (Social Protection Management Information System)
- **Supporting**: Employer Systems, Employment Registry, Tax Authority

### **Trigger**
- Benefit eligibility assessment required
- Employment status change reported
- Periodic compliance verification
- Cross-system data reconciliation
- Fraud prevention investigation

### **Objective**
Enable real-time or batch verification of employment status to:
- Prevent duplicate benefit payments
- Verify benefit eligibility conditions
- Detect unreported employment changes
- Support targeting and means testing
- Ensure compliance with work obligations

## 🔄 **Process Flow Design (DCI Pattern)**

### **Process Flow 2: Employment Status Verification**
**File**: `processflow2req.json` / `processflow2res.json`
**Pattern**: Social Registry Search (adapted)

#### **Request Structure**
```json
{
  "signature": "DCI standard signature",
  "header": {
    "version": "1.0.0",
    "message_id": "550e8400-e29b-41d4-a716-446655440010",
    "message_ts": "2025-09-15T14:30:00.000Z",
    "action": "status_verify",
    "sender_id": "SP-MIS-CHILE",
    "receiver_id": "PES-SENCE",
    "total_count": "1",
    "is_msg_encrypted": false
  },
  "message": {
    "transaction_id": "550e8400-e29b-41d4-a716-446655440011",
    "employment_status_request": [
      {
        "reference_id": "550e8400-e29b-41d4-a716-446655440012",
        "timestamp": "2025-09-15T14:30:00.000Z",
        "verification_criteria": {
          "version": "1.0.0",
          "verification_type": "benefit_eligibility_check",
          "person": {
            "@context": "https://schema.spdci.org/employment/v1/",
            "@type": "Person",
            "identifiers": [
              {
                "identifier_type": "national_id",
                "identifier_value": "12345678901"
              }
            ],
            "name": {
              "given_name": "Carlos",
              "surname": "Silva"
            },
            "birth_date": "1980-07-22"
          },
          "verification_period": {
            "start_date": "2025-07-01",
            "end_date": "2025-09-30"
          },
          "verification_scope": [
            "current_employment_status",
            "employment_history",
            "benefit_eligibility",
            "income_information"
          ],
          "requesting_programme": {
            "@type": "ibr:Programme",
            "programme_identifier": "UI-BENEFIT-2024",
            "programme_name": "Unemployment Insurance",
            "implementing_institution": "AFC Chile"
          },
          "legal_basis": "unemployment_insurance_law_2023",
          "verification_purpose": "benefit_continuation_assessment"
        },
        "consent": {
          "consent_given": true,
          "consent_date": "2025-09-14T10:00:00.000Z",
          "consent_type": "employment_verification",
          "legal_basis": "unemployment_insurance_law_2023"
        },
        "locale": "es-CL"
      }
    ]
  }
}
```

#### **Response Structure**
```json
{
  "signature": "DCI standard signature",
  "header": {
    "version": "1.0.0",
    "message_id": "550e8400-e29b-41d4-a716-446655440013",
    "message_ts": "2025-09-15T14:30:02.000Z",
    "action": "status_response",
    "sender_id": "PES-SENCE",
    "receiver_id": "SP-MIS-CHILE",
    "total_count": "1",
    "is_msg_encrypted": false
  },
  "message": {
    "transaction_id": "550e8400-e29b-41d4-a716-446655440011",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440014",
    "employment_status_response": [
      {
        "reference_id": "550e8400-e29b-41d4-a716-446655440012",
        "timestamp": "2025-09-15T14:30:02.000Z",
        "status": "succ",
        "status_reason_code": "VERIFICATION_COMPLETE",
        "status_reason_message": "Employment status verification completed successfully",
        "data": {
          "verification_id": "550e8400-e29b-41d4-a716-446655440015",
          "current_employment_status": {
            "employment_status": "unemployed",
            "status_effective_date": "2025-08-15",
            "last_employment_end_date": "2025-08-14",
            "unemployment_duration_days": 32,
            "job_search_status": "active",
            "job_search_activities": [
              {
                "activity_type": "job_application",
                "activity_date": "2025-09-12",
                "employer_name": "Empresa ABC",
                "application_method": "online_platform"
              }
            ]
          },
          "employment_history": [
            {
              "employer_name": "Constructora XYZ",
              "employment_start_date": "2024-01-15",
              "employment_end_date": "2025-08-14",
              "employment_type": "full_time",
              "termination_reason": "economic_reasons",
              "industry_code": "construction",
              "average_monthly_income": 450000
            }
          ],
          "benefit_eligibility": {
            "unemployment_insurance_eligible": true,
            "eligibility_period_months": 5,
            "monthly_benefit_amount": 225000,
            "benefits_remaining_months": 3,
            "compliance_status": "compliant",
            "next_verification_date": "2025-10-15"
          },
          "verification_sources": [
            {
              "source_system": "sence_employment_registry",
              "data_points": ["employment_status", "job_search_activities"],
              "last_updated": "2025-09-15T14:00:00.000Z"
            },
            {
              "source_system": "sii_tax_records",
              "data_points": ["employment_history", "income_information"],
              "last_updated": "2025-09-10T09:00:00.000Z"
            }
          ]
        },
        "locale": "es-CL"
      }
    ]
  }
}
```

## 🌍 **Country Implementation Variants**

### **Process Flow 2-South Korea: KEIS-NBLSS Integration Model**
**File**: `processflow2-korea-req.json`
**Specific Features**:
- Real-time employment insurance database verification
- Multi-system welfare benefit coordination
- Employment Welfare Plus Center integration

**Additional Data Elements**:
```json
{
  "korea_specific": {
    "employment_insurance": {
      "ei_qualification_status": "qualified",
      "ei_contribution_months": 18,
      "ei_benefit_rate": 60,
      "ei_daily_benefit_amount": 45000
    },
    "welfare_integration": {
      "nblss_case_number": "NBLSS-2025-005678",
      "integrated_services": ["employment_services", "vocational_training", "childcare_support"],
      "case_manager": "Kim Min-jun"
    },
    "verification_method": "real_time_api"
  }
}
```

### **Process Flow 2-South Africa: SASSA-UIF Coordination Model**
**File**: `processflow2-southafrica-req.json`
**Specific Features**:
- SASSA social grant eligibility verification
- UIF employment history cross-reference
- Means test income verification

### **Process Flow 2-Australia: Centrelink Integration Model**
**File**: `processflow2-australia-req.json`
**Specific Features**:
- JobSeeker Payment eligibility verification
- Australian Taxation Office income matching
- Mutual obligation compliance tracking

## 📋 **Data Objects Mapping**

### **Primary Data Object**: `DO.EMPL.02 — Employment Status Verification`
- **Person Information**: Uses `DO.COM.Person` with verification-specific attributes
- **Employment Status**: Current status with effective dates and duration
- **Employment History**: Previous employment records with income data
- **Benefit Eligibility**: Entitlement calculations and compliance status

### **Code Directories Used**:
- `CD.EMPL.01 — EmploymentStatus`: unemployed, employed, underemployed, etc.
- `CD.EMPL.03 — VerificationType`: benefit_eligibility_check, compliance_verification, etc.
- `CD.COM.01 — RequestStatus`: rcvd, pdng, succ, rjct

## 🔧 **API Implementation**

### **Endpoint Mapping**
```
POST /employment-status/verify → processflow2 (Status Verification)
├── Standard implementation: processflow2req.json
├── South Korea variant: processflow2-korea-req.json
├── South Africa variant: processflow2-southafrica-req.json
└── Australia variant: processflow2-australia-req.json
```

### **Business Rules**
1. **Data Source Validation**:
   - Employment status must be verified from authoritative sources
   - Income information cross-referenced with tax records
   - Job search activities validated through PES systems

2. **Temporal Constraints**:
   - Verification period must not exceed 12 months
   - Employment status effective dates must be consistent
   - Historical data accuracy depends on source system retention

3. **Privacy Protection**:
   - Verification limited to specific legal purposes
   - Employer information anonymized where legally required
   - Income details provided at appropriate aggregation level

4. **Real-time Requirements**:
   - Critical eligibility checks completed within 5 seconds
   - Batch verification processes completed within 24 hours
   - Error responses provided with specific retry guidance

## 📊 **Success Metrics & KPIs**

### **Process Efficiency**
- **Verification Response Time**: < 5 seconds for real-time checks (South Korea: 2.1s avg, Chile: 3.4s avg)
- **Batch Processing Time**: < 4 hours for large verification runs
- **Data Accuracy Rate**: > 98% for employment status verification (South Korea: 99.2%, Chile: 98.7%)

### **Quality Indicators**
- **Source Data Completeness**: > 95% of verifications with complete data (Chile RSH: 87% administrative data)
- **Cross-system Consistency**: < 2% discrepancy rate between systems (South Korea Work24: 0.8%)
- **False Positive Rate**: < 1% for eligibility determinations (South Korea AI-enhanced: 0.3%)

### **Real-World Performance (2024 Data)**
**South Korea Work24 System**:
- 4.2 million unemployment benefit claims processed
- 99.2% automated eligibility verification accuracy
- 0.8% fraud detection rate with AI enhancement
- 2.1 seconds average verification response time

**Chile RSH-SENCE Integration**:
- 694,245 employment subsidy beneficiaries verified
- 98.7% cross-system data consistency
- 24-hour average processing for new applications
- 87% verification based on administrative data

### **Compliance Tracking**
- **Legal Basis Documentation**: 100% of verifications have valid legal basis
- **Consent Coverage**: 100% of verifications have documented consent
- **Audit Trail Completeness**: 100% of transactions logged with correlation IDs

## ⚠️ **Implementation Challenges**

### **Technical Challenges**
1. **Data Synchronization**: Employment status changes occur across multiple systems (South Korea solved with Work24 unified platform)
2. **Legacy System Integration**: Older systems may lack real-time API capabilities (Chile addressed through Government Interoperability Platform)
3. **Performance Requirements**: High-volume verification processing demands (South Korea processes 4.2M claims annually)
4. **AI Integration Complexity**: Implementing machine learning for fraud detection and job matching (South Korea's experience with WorkNet AI)

### **Governance Challenges**
1. **Data Quality Standards**: Inconsistent data formats across employment systems
2. **Inter-agency Coordination**: Different update frequencies and business rules
3. **Privacy Regulations**: Varying data protection requirements by jurisdiction

### **Operational Challenges**
1. **Fraud Detection**: Sophisticated methods needed to detect unreported employment
2. **Appeal Processes**: Mechanisms needed for disputing verification results
3. **System Availability**: High uptime requirements for critical benefit decisions

## 🎯 **Implementation Recommendations**

### **Phase 1: Core Integration** (Weeks 1-3)
1. Establish data sharing agreements between employment and SP agencies
2. Implement basic status verification API following DCI pattern
3. Create test environment with sample verification scenarios

### **Phase 2: Enhanced Verification** (Weeks 4-6)
1. Add employment history and income verification capabilities
2. Implement real-time verification for critical use cases
3. Develop fraud detection algorithms and suspicious activity alerts

### **Phase 3: Multi-source Integration** (Weeks 7-9)
1. Integrate tax authority and employer systems for comprehensive verification
2. Implement job search activity tracking and compliance monitoring
3. Add appeal and dispute resolution workflows

### **Phase 4: Advanced Analytics** (Weeks 10-12)
1. Deploy predictive analytics for benefit eligibility forecasting
2. Implement automated compliance monitoring and intervention triggers
3. Establish comprehensive reporting and audit capabilities

---

**Next**: [PRS.EMPL.03 — Training Benefits](./prs-empl-03-training-benefits.md)