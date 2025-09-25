# Employment-SP Data Objects

This document defines data objects specific to Employment-Social Protection interoperability, extending DCI common patterns.

## DO.EMPL.01  Employment Referral

**Description**: A referral from Social Protection systems to Public Employment Services for work-able beneficiaries.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/common/v1/",
    "empl": "https://schema.spdci.org/employment/v1/",
    "ibr": "https://schema.spdci.org/extensions/ibr/v1/"
  },
  "@type": "empl:EmploymentReferral",
  "referral_id": "550e8400-e29b-41d4-a716-446655440000",
  "person": {
    "@type": "Person",
    "@id": "person:12345",
    "identifiers": [
      {
        "@type": "Identifier",
        "identifier_type": "national_id",
        "identifier_value": "12345678901"
      }
    ],
    "name": {
      "@type": "Name",
      "given_name": "Maria",
      "surname": "Rodriguez"
    }
  },
  "source_programme": {
    "@type": "ibr:Programme",
    "programme_identifier": "SP-CASH-2024",
    "programme_name": "Family Emergency Income",
    "implementing_institution": "Ministry of Social Development"
  },
  "referral_reason": "unemployment_benefit_activation",
  "work_capacity_assessment": {
    "assessment_date": "2025-09-10",
    "work_able": true,
    "restrictions": ["part_time_only"],
    "skills": ["basic_literacy", "customer_service"],
    "work_preferences": {
      "preferred_sectors": ["retail", "services"],
      "geographic_mobility": "local_only",
      "availability": "immediate"
    }
  },
  "referral_date": "2025-09-15T14:30:00.000Z",
  "target_pes_office": "PES-SANTIAGO-CENTRO",
  "status": "pending",
  "compliance_requirements": {
    "job_search_obligation": true,
    "training_participation": "recommended",
    "reporting_frequency": "monthly"
  },
  "created_by": "sp-mis-system",
  "created_at": "2025-09-15T14:30:00.000Z"
}
```

**Constraints**:
- referral_id MUST be UUID v4 format
- person MUST reference valid `DO.COM.Person` object
- referral_reason MUST use values from `CD.EMPL.ReferralReason`
- status MUST use values from `CD.COM.RequestStatus`
- All timestamps MUST use ISO 8601 UTC format

**Business rules**:
- Person must be assessed as work-able before referral
- Source programme must be active SP programme
- Target PES office must be within person's geographic area
- Compliance requirements must align with programme conditions

**Governance**:
- Data minimization: Only share data necessary for employment services
- Consent: Explicit consent required for employment service participation
- Retention: 5 years or until programme completion, whichever is longer

---

## DO.EMPL.02  Employment Status Verification

**Description**: Employment status information shared between PES and SP systems for benefit compliance verification.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/common/v1/",
    "empl": "https://schema.spdci.org/employment/v1/",
    "social": "https://schema.spdci.org/extensions/social/v1/"
  },
  "@type": "empl:EmploymentStatusVerification",
  "verification_id": "550e8400-e29b-41d4-a716-446655440001",
  "person_id": "person:12345",
  "employment_status": "employed",
  "employment_details": {
    "employer_name": "Tech Solutions SPA",
    "employer_tax_id": "96.123.456-7",
    "job_title": "Administrative Assistant",
    "employment_type": "permanent_full_time",
    "start_date": "2025-08-01",
    "end_date": null,
    "monthly_income": {
      "@type": "Currency",
      "amount": "450000",
      "currency_code": "CLP"
    },
    "work_hours_per_week": 40
  },
  "verification_source": "employer_declaration",
  "verification_date": "2025-09-15",
  "verification_method": "electronic_integration",
  "confidence_level": "high",
  "verified_by": "labor-directorate-system",
  "valid_until": "2025-12-15",
  "compliance_notes": "Employed above minimum wage threshold, benefit suspension triggered"
}
```

**Constraints**:
- employment_status MUST use values from `CD.EMPL.EmploymentStatus`
- employment_type MUST use values from `CD.EMPL.EmploymentType`
- verification_source MUST use values from `CD.EMPL.VerificationSource`
- confidence_level MUST use values from `CD.COM.VerificationStatus`

**Business rules**:
- Employment status must be verified within 30 days for accuracy
- Income verification required for means-tested benefit eligibility
- Employment changes must trigger automatic SP system notifications
- High-confidence verifications valid for 3 months, others for 1 month

---

## DO.EMPL.03  Employment Benefit

**Description**: Employment-related benefits provided through SP-PES coordination, extending IBR Benefit structure.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/common/v1/",
    "empl": "https://schema.spdci.org/employment/v1/",
    "ibr": "https://schema.spdci.org/extensions/ibr/v1/"
  },
  "@type": "empl:EmploymentBenefit",
  "benefit_id": "550e8400-e29b-41d4-a716-446655440002",
  "person_id": "person:12345",
  "benefit_type": "training",
  "employment_benefit_subtype": "vocational_skills_training",
  "benefit_description": "Digital Marketing Skills Training Program",
  "training_details": {
    "training_provider": "Professional Institute ABC",
    "course_name": "Digital Marketing Fundamentals",
    "duration_weeks": 12,
    "schedule": "part_time_evening",
    "certification_type": "industry_recognized",
    "expected_outcome": "employment_placement"
  },
  "benefit_value": {
    "@type": "Currency",
    "amount": "150000",
    "currency_code": "CLP"
  },
  "stipend": {
    "@type": "Currency",
    "amount": "50000",
    "currency_code": "CLP",
    "frequency": "monthly"
  },
  "start_date": "2025-10-01",
  "end_date": "2025-12-20",
  "attendance_requirement": 0.80,
  "performance_requirement": "pass_final_assessment",
  "employment_placement_support": {
    "job_matching_included": true,
    "placement_target_days": 30,
    "follow_up_period_months": 6
  },
  "compliance_tracking": {
    "attendance_rate": null,
    "performance_score": null,
    "completion_status": "enrolled"
  }
}
```

**Constraints**:
- benefit_type MUST use values from `CD.EMPL.BenefitType` (extends IBR BenefitTypeEnum)
- employment_benefit_subtype MUST use values from `CD.EMPL.TrainingType`
- completion_status MUST use values from `CD.EMPL.CompletionStatus`
- attendance_requirement MUST be decimal between 0.0 and 1.0

**Business rules**:
- Training benefits require signed participation agreement
- Stipend eligibility depends on attendance and performance requirements
- Employment placement support mandatory for vocational training benefits
- Completion status updates trigger SP benefit continuation/graduation decisions

---

## DO.EMPL.04  Job Placement

**Description**: Job placement events resulting from PES services, shared with SP systems for benefit management.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/common/v1/",
    "empl": "https://schema.spdci.org/employment/v1/"
  },
  "@type": "empl:JobPlacement",
  "placement_id": "550e8400-e29b-41d4-a716-446655440003",
  "person_id": "person:12345",
  "referral_id": "550e8400-e29b-41d4-a716-446655440000",
  "employer": {
    "employer_name": "Local Retail Chain SA",
    "employer_tax_id": "76.987.654-3",
    "sector": "retail",
    "company_size": "medium"
  },
  "job_details": {
    "job_title": "Sales Associate",
    "job_category": "sales_service",
    "employment_type": "permanent_full_time",
    "contract_duration": null,
    "probation_period_days": 90,
    "work_location": "Santiago, Providencia",
    "remote_work_option": false
  },
  "compensation": {
    "base_salary": {
      "@type": "Currency",
      "amount": "420000",
      "currency_code": "CLP"
    },
    "variable_compensation": {
      "@type": "Currency",
      "amount": "50000",
      "currency_code": "CLP",
      "type": "monthly_commission"
    },
    "benefits_package": ["health_insurance", "meal_allowance"]
  },
  "placement_date": "2025-10-15",
  "start_date": "2025-10-22",
  "placement_method": "pes_job_matching",
  "pes_support_provided": [
    "cv_preparation",
    "interview_coaching",
    "employer_negotiation"
  ],
  "follow_up_schedule": {
    "initial_check": "2025-11-22",
    "probation_review": "2026-01-22",
    "six_month_review": "2026-04-22"
  },
  "sp_benefit_impact": {
    "benefit_continuation": false,
    "graduation_triggered": true,
    "income_supplement_eligible": false
  }
}
```

**Constraints**:
- employment_type MUST use values from `CD.EMPL.EmploymentType`
- job_category MUST use values from `CD.EMPL.JobCategory`
- placement_method MUST use values from `CD.EMPL.PlacementMethod`
- All currency amounts MUST be positive values

**Business rules**:
- Job placement must be verified within 7 days of reported start date
- Income level determines SP benefit graduation or continuation
- Follow-up schedule mandatory for sustainable employment tracking
- Placement failure within probation period triggers re-engagement protocols

---

## DO.EMPL.05  Compliance Monitoring

**Description**: Tracking compliance with employment-related obligations for SP benefit recipients.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/common/v1/",
    "empl": "https://schema.spdci.org/employment/v1/"
  },
  "@type": "empl:ComplianceMonitoring",
  "monitoring_id": "550e8400-e29b-41d4-a716-446655440004",
  "person_id": "person:12345",
  "monitoring_period": {
    "start_date": "2025-09-01",
    "end_date": "2025-11-30"
  },
  "obligations": [
    {
      "obligation_type": "job_search",
      "requirement": "minimum_4_applications_per_month",
      "status": "compliant",
      "evidence": [
        {
          "application_date": "2025-09-05",
          "employer": "Company A",
          "position": "Administrative Assistant"
        }
      ]
    },
    {
      "obligation_type": "pes_appointments",
      "requirement": "monthly_caseworker_meeting",
      "status": "non_compliant",
      "missed_appointments": 1,
      "next_appointment": "2025-10-15T10:00:00.000Z"
    },
    {
      "obligation_type": "training_participation",
      "requirement": "80_percent_attendance",
      "status": "compliant",
      "current_attendance_rate": 0.85
    }
  ],
  "overall_compliance_score": 0.67,
  "compliance_status": "partial_compliance",
  "enforcement_actions": [
    {
      "action_type": "warning_letter",
      "action_date": "2025-09-20",
      "reason": "missed_pes_appointment"
    }
  ],
  "next_review_date": "2025-10-15",
  "caseworker_id": "caseworker:789"
}
```

**Constraints**:
- obligation_type MUST use values from `CD.EMPL.ObligationType`
- compliance_status MUST use values from `CD.EMPL.ComplianceStatus`
- overall_compliance_score MUST be decimal between 0.0 and 1.0
- enforcement_actions MUST use values from `CD.EMPL.EnforcementAction`

**Business rules**:
- Compliance monitoring required for all work-able SP beneficiaries
- Non-compliance triggers graduated enforcement actions
- Three consecutive non-compliance periods may result in benefit suspension
- Compliance score calculation includes weighted obligation fulfillment

---

## Open Issues

`TODO(evidence)`: Validate data structures against country-specific PES-SP integration requirements.

`TODO(schema)`: Generate formal JSON-LD context definitions for employment interface objects.

`TODO(api)`: Define API methods for CRUD operations on employment data objects.