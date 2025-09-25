# Employment-SP API Message Structure

This document defines the message structure patterns and data formats for Employment-Social Protection API communications, following DCI standards and employment domain requirements.

## DCI Three-Part Message Structure

### Employment-SP Request Message Pattern

All Employment-SP API requests follow the **DCI three-part structure** (signature + header + message) with employment-specific extensions:

```json
{
  "signature": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJzcC1taXMtY2wiLCJpYXQiOjE2OTQ3ODQwMDAsImV4cCI6MTY5NDc4NzYwMH0.signature_bytes_base64",
  "header": {
    "message_id": "550e8400-e29b-41d4-a716-446655440000",
    "message_ts": "2025-09-15T14:30:00.000Z",
    "action": "referral",
    "sender_id": "SP-MIS-CL",
    "receiver_id": "PES-CL",
    "sender_uri": "https://api-cl.sp-mis.gov/v1",
    "total_count": 1,
    "completed_count": 0,
    "meta": {
      "employment_context": {
        "purpose_of_use": "employment_service_provision",
        "legal_basis": "social_protection_law_2023_article_15",
        "data_minimization_level": "service_necessary"
      }
    }
  },
  "message": {
    "encrypted": "eyJhbGciOiJSU0EtT0FFUC0yNTYiLCJlbmMiOiJBMjU2R0NNIn0.encrypted_key.iv.ciphertext.tag"
  }
}
```

**Decrypted Message Payload** (JWE decrypted content):
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440001",
  "referral_data": {
    "person_reference": "SP_REF_12345",
    "source_programme": {
      "@type": "spdci:Programme",
      "programme_identifier": "IBR-CASH-2024",
      "programme_name": "Ingreso Familiar de Emergencia"
    },
    "referral_reason": "unemployment_activation",
    "target_pes_jurisdiction": "REGION_METROPOLITANA",
    "compliance_requirements": {
      "job_search_obligation": true,
      "reporting_frequency": "monthly",
      "training_participation": false
    },
    "consent": {
      "consent_given": true,
      "consent_timestamp": "2025-09-15T14:00:00.000Z",
      "data_retention_period": "P2Y",
      "consent_type": "employment_services",
      "legal_basis": "social_protection_law_2023"
    }
  },
  "locale": "es-CL"
}
```

### Employment-SP Response Message Pattern

All Employment-SP API responses follow the **DCI three-part response structure**:

```json
{
  "signature": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.response_signature_payload.signature_bytes",
  "header": {
    "message_id": "550e8400-e29b-41d4-a716-446655440002",
    "message_ts": "2025-09-15T14:30:05.000Z",
    "action": "referral",
    "status": "succ",
    "sender_id": "PES-CL",
    "receiver_id": "SP-MIS-CL",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440000"
  },
  "message": {
    "encrypted": "eyJhbGciOiJSU0EtT0FFUC0yNTYiLCJlbmMiOiJBMjU2R0NNIn0.response_encrypted_content"
  }
}
```

**Decrypted Response Payload**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440001",
  "referral_id": "PES-REF-2025-001234",
  "status": "accepted",
  "pes_case_number": "CL-PES-2025-001234",
  "assigned_caseworker": "CW-SANTIAGO-456",
  "next_contact_date": "2025-09-20",
  "processing_metadata": {
    "processed_at": "2025-09-15T14:30:04.000Z",
    "processing_system": "pes-casework-module-cl",
    "validation_passed": true,
    "business_rules_applied": ["work_capacity_validation", "geographic_jurisdiction"]
  }
}
```

---

## JSON-LD Semantic Structure

### Employment Context Definition

Employment-SP APIs use JSON-LD for semantic interoperability:

```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
    "social": "https://schema.spdci.org/extensions/social/v1/",
    "empl": "https://schema.spdci.org/extensions/employment/v1/",
    "common": "https://schema.spdci.org/common/v1/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "owl": "http://www.w3.org/2002/07/owl#",

    "Programme": "spdci:Programme",
    "Beneficiary": "spdci:Beneficiary",
    "Benefit": "spdci:Benefit",
    "Group": "social:Group",
    "Member": "social:Member",

    "EmploymentReferral": "empl:EmploymentReferral",
    "EmploymentVerification": "empl:EmploymentVerification",
    "JobPlacement": "empl:JobPlacement",
    "ComplianceStatus": "empl:ComplianceStatus",

    "programme_identifier": {
      "@id": "spdci:programme_identifier",
      "@type": "xsd:string"
    },
    "referral_reason": {
      "@id": "empl:referral_reason",
      "@type": "empl:ReferralReasonEnum"
    },
    "employment_status": {
      "@id": "empl:employment_status",
      "@type": "empl:EmploymentStatusEnum"
    },
    "compliance_requirements": {
      "@id": "empl:compliance_requirements",
      "@type": "empl:ComplianceRequirements"
    },
    "enrollment_date": {
      "@id": "spdci:enrollment_date",
      "@type": "common:DateTime"
    },
    "enrollment_status": {
      "@id": "spdci:enrollment_status",
      "@type": "spdci:EnrollementStatusEnum"
    }
  }
}
```

### Namespace Definitions

**Core DCI Namespaces** (following existing DCI patterns):
- `spdci`: DCI Integrated Beneficiary Registry (IBR) extensions
- `social`: DCI Social Registry data objects and properties
- `empl`: Employment-specific extensions to DCI schemas
- `common`: Common DCI data types and structures

**Country-Specific Extensions** (extending DCI namespaces):
- `cl-empl`: Chile-specific employment extensions (RSH-SENCE integration)
- `kr-empl`: South Korea extensions (Work24 platform integration)
- `de-empl`: Germany extensions (SGB framework compliance)
- `au-empl`: Australia extensions (JobActive provider network)
- `tr-empl`: Turkey extensions (İŞKUR-ISAF coordination)

---

## Employment Data Object Structures

### Employment Referral Message

```json
{
  "@context": "https://schema.spdci.org/employment/v1/context.json",
  "@type": "EmploymentReferral",
  "referral_id": "550e8400-e29b-41d4-a716-446655440000",
  "person": {
    "@type": "Person",
    "@id": "person:SP_REF_12345",
    "identifiers": [
      {
        "@type": "Identifier",
        "identifier_type": "system_reference",
        "identifier_value": "SP_REF_12345",
        "issuing_system": "sp-mis",
        "verification_status": "verified"
      }
    ],
    "name": {
      "@type": "Name",
      "given_name": "María",
      "surname": "González",
      "full_name": "María González"
    },
    "demographics": {
      "age_range": "25-35",
      "education_level": "secondary_complete",
      "primary_language": "es"
    }
  },
  "source_programme": {
    "@type": "Programme",
    "programme_identifier": "CL-IFE-2024",
    "programme_name": "Ingreso Familiar de Emergencia",
    "implementing_institution": "MIDESO",
    "programme_type": "emergency_cash_transfer"
  },
  "referral_details": {
    "referral_reason": "work_search_obligation",
    "referral_date": "2025-09-15T14:30:00.000Z",
    "target_pes_office": "OMILSantiago-Centro",
    "urgency_level": "standard",
    "expected_response_time": "5_working_days"
  },
  "work_capacity_assessment": {
    "assessment_date": "2025-09-10",
    "work_able": true,
    "availability": "full_time",
    "mobility": "local_transport_available",
    "restrictions": ["no_night_shifts"],
    "skills": [
      {
        "skill_category": "administrative",
        "proficiency_level": "intermediate",
        "certified": false
      },
      {
        "skill_category": "customer_service",
        "proficiency_level": "advanced",
        "certified": true,
        "certification_source": "previous_employment"
      }
    ]
  },
  "compliance_requirements": {
    "job_search_obligation": {
      "required": true,
      "minimum_applications_per_month": 4,
      "documentation_required": true
    },
    "pes_appointments": {
      "frequency": "monthly",
      "mandatory": true,
      "missed_appointment_consequences": "benefit_review"
    },
    "training_participation": {
      "recommendation_level": "encouraged",
      "barrier_assessment_required": true
    }
  },
  "consent": {
    "consent_given": true,
    "consent_date": "2025-09-14T10:00:00.000Z",
    "consent_scope": ["employment_service_provision", "job_matching", "training_opportunities"],
    "legal_basis": "Law_20379_article_12",
    "data_sharing_period": "24_months",
    "withdrawal_procedure": "contact_sp_office"
  },
  "created_by": "sp-mis-system",
  "created_at": "2025-09-15T14:30:00.000Z"
}
```

### Employment Status Verification Message

```json
{
  "@context": "https://schema.spdci.org/employment/v1/context.json",
  "@type": "EmploymentStatusVerification",
  "verification_id": "550e8400-e29b-41d4-a716-446655440001",
  "person_reference": {
    "@type": "PersonReference",
    "system_id": "PES_ID_67890",
    "cross_reference": "SP_REF_12345",
    "verification_method": "cross_system_lookup"
  },
  "employment_status": {
    "current_status": "employed",
    "status_effective_date": "2025-08-01",
    "verification_date": "2025-09-15",
    "confidence_level": "high",
    "verification_sources": [
      {
        "source_type": "employer_payroll",
        "source_name": "TechSolutions_SPA",
        "verification_method": "electronic_integration",
        "last_updated": "2025-09-14"
      },
      {
        "source_type": "tax_authority",
        "source_name": "SII_Chile",
        "verification_method": "monthly_declaration",
        "last_updated": "2025-09-10"
      }
    ]
  },
  "employment_details": {
    "employer": {
      "employer_name": "TechSolutions SPA",
      "employer_tax_id": "96.XXX.XXX-X",
      "sector": "information_technology",
      "company_size": "medium_enterprise",
      "location": {
        "region": "RM",
        "commune": "Las_Condes"
      }
    },
    "position": {
      "job_title": "Administrative Assistant",
      "job_category": "ISCO_4120",
      "employment_type": "permanent_full_time",
      "contract_type": "indefinite_term",
      "start_date": "2025-08-01",
      "probation_period_complete": true
    },
    "compensation": {
      "base_salary": {
        "@type": "Currency",
        "amount": "450000",
        "currency_code": "CLP",
        "frequency": "monthly"
      },
      "benefits": ["health_insurance", "meal_allowance"],
      "total_compensation_range": "450000-500000_CLP"
    },
    "work_schedule": {
      "hours_per_week": 40,
      "schedule_type": "regular_business_hours",
      "flexibility": "standard_schedule"
    }
  },
  "sp_benefit_impact": {
    "current_benefit_status": "active",
    "impact_assessment": {
      "income_threshold_exceeded": true,
      "graduation_recommended": true,
      "transition_support_needed": false
    },
    "recommended_actions": [
      {
        "action": "benefit_graduation",
        "timeline": "immediate",
        "supporting_services": ["employment_retention_counseling"]
      }
    ],
    "next_review_date": "2025-12-15"
  },
  "data_quality": {
    "completeness_score": 0.95,
    "accuracy_confidence": "high",
    "timeliness": "current",
    "source_reliability": "verified"
  },
  "verified_by": "PES_verification_service",
  "verification_timestamp": "2025-09-15T16:45:00.000Z"
}
```

### Training Benefit Enrollment Message

```json
{
  "@context": "https://schema.spdci.org/employment/v1/context.json",
  "@type": "EmploymentBenefit",
  "benefit_id": "550e8400-e29b-41d4-a716-446655440002",
  "person_reference": "SP_REF_12345",
  "benefit_classification": {
    "benefit_type": "training",
    "benefit_subtype": "vocational_skills_training",
    "training_category": "digital_skills",
    "qualification_level": "intermediate"
  },
  "training_programme": {
    "programme_name": "Digital Marketing Fundamentals",
    "programme_code": "SENCE_DM_2024_015",
    "training_provider": {
      "provider_name": "Instituto Profesional ABC",
      "provider_code": "SENCE_PROV_1234",
      "accreditation": "SENCE_certified",
      "provider_type": "private_institution"
    },
    "curriculum": {
      "duration_hours": 120,
      "duration_weeks": 12,
      "mode_of_delivery": "hybrid",
      "schedule": "part_time_evening",
      "location": "Santiago_Centro",
      "online_component_percentage": 40
    },
    "learning_outcomes": [
      "Digital marketing strategy development",
      "Social media campaign management",
      "Analytics and performance measurement",
      "Content creation and optimization"
    ],
    "certification": {
      "certification_type": "industry_recognized",
      "issuing_body": "SENCE",
      "credential_name": "Digital Marketing Professional Certificate",
      "validity_period": "lifetime"
    }
  },
  "financial_details": {
    "total_cost": {
      "@type": "Currency",
      "amount": "150000",
      "currency_code": "CLP"
    },
    "funding_breakdown": [
      {
        "funding_source": "SENCE_training_fund",
        "amount": "120000",
        "percentage": 80
      },
      {
        "funding_source": "participant_contribution",
        "amount": "30000",
        "percentage": 20
      }
    ],
    "stipend": {
      "@type": "Currency",
      "amount": "50000",
      "currency_code": "CLP",
      "frequency": "monthly",
      "conditions": ["80_percent_attendance", "satisfactory_progress"]
    }
  },
  "enrollment_details": {
    "enrollment_date": "2025-09-20",
    "start_date": "2025-10-01",
    "end_date": "2025-12-20",
    "enrollment_status": "confirmed",
    "class_size": 25,
    "position_in_waitlist": null
  },
  "requirements_and_expectations": {
    "attendance_requirement": 0.80,
    "assessment_requirements": [
      "weekly_practical_assignments",
      "midterm_project",
      "final_portfolio_presentation"
    ],
    "completion_criteria": {
      "minimum_attendance": "80_percent",
      "minimum_grade": "70_percent",
      "practical_demonstration": "required"
    },
    "support_services": [
      "academic_tutoring",
      "career_counseling",
      "job_placement_assistance"
    ]
  },
  "employment_integration": {
    "job_placement_support": true,
    "placement_target_timeline": "30_days_post_completion",
    "employer_partnerships": [
      "Digital_marketing_agencies",
      "E-commerce_companies",
      "Small_business_sector"
    ],
    "expected_employment_outcomes": {
      "placement_probability": 0.75,
      "expected_salary_range": "400000-600000_CLP",
      "career_progression_potential": "medium_to_high"
    }
  },
  "monitoring_and_evaluation": {
    "progress_tracking": {
      "frequency": "weekly",
      "metrics": ["attendance_rate", "assignment_completion", "skill_development"],
      "reporting_system": "SENCE_training_portal"
    },
    "milestone_reviews": [
      {
        "milestone": "month_1_assessment",
        "date": "2025-11-01",
        "criteria": "basic_concepts_mastery"
      },
      {
        "milestone": "month_2_project",
        "date": "2025-12-01",
        "criteria": "practical_application_demonstration"
      }
    ]
  },
  "authorization": {
    "approved_by": "PES_training_coordinator",
    "approval_date": "2025-09-18T14:00:00.000Z",
    "budget_allocation": "SENCE_2024_training_budget",
    "approval_reference": "SENCE_APPR_2024_5678"
  },
  "created_at": "2025-09-20T10:30:00.000Z"
}
```

---

## Message Validation and Quality Assurance

### Schema Validation

**JSON Schema Enforcement**:
- All messages validated against employment-specific JSON schemas
- Schema validation includes business rule checking
- Nested object validation for complex employment structures
- Cross-reference validation between related objects

**Employment Business Rules**:
```json
{
  "validation_rules": {
    "referral_creation": [
      "person_must_be_work_able",
      "source_programme_must_be_active",
      "target_pes_office_must_serve_geographic_area",
      "consent_required_for_employment_services"
    ],
    "employment_verification": [
      "verification_sources_must_be_reliable",
      "income_data_must_be_current_within_30_days",
      "employment_status_must_be_consistent_across_sources"
    ],
    "benefit_enrollment": [
      "person_must_meet_training_prerequisites",
      "training_provider_must_be_accredited",
      "budget_allocation_must_be_available"
    ]
  }
}
```

### Data Quality Metrics

**Completeness Scoring**:
```json
{
  "data_quality": {
    "completeness_score": 0.95,
    "required_fields_present": 19,
    "total_required_fields": 20,
    "missing_fields": ["emergency_contact"],
    "quality_threshold": 0.90,
    "quality_status": "acceptable"
  }
}
```

**Accuracy Validation**:
```json
{
  "accuracy_validation": {
    "cross_system_consistency": "verified",
    "data_freshness": "current",
    "source_reliability": "high",
    "validation_timestamp": "2025-09-15T16:45:00.000Z",
    "validation_method": "automated_cross_reference"
  }
}
```

---

## Error and Exception Handling

### Employment-Specific Error Codes

```json
{
  "error": {
    "code": "EMPL_VALIDATION_ERROR",
    "message": "Employment referral validation failed",
    "category": "business_rule_violation",
    "severity": "error",
    "details": [
      {
        "field": "work_capacity_assessment.work_able",
        "error_code": "WORK_CAPACITY_REQUIRED",
        "message": "Person must be assessed as work-able before referral creation",
        "constraint": "work_able = true",
        "received_value": false
      }
    ],
    "documentation_url": "https://docs.employment-sp.dci.gov/errors/EMPL_VALIDATION_ERROR",
    "suggested_actions": [
      "Complete work capacity assessment",
      "Update person record with assessment results",
      "Retry referral creation"
    ]
  }
}
```

### Retry and Recovery Patterns

**Idempotency Support**:
```json
{
  "idempotency": {
    "idempotency_key": "EMPL_REF_2025091514301234",
    "operation_type": "referral_creation",
    "original_timestamp": "2025-09-15T14:30:00.000Z",
    "retry_count": 1,
    "max_retries": 3
  }
}
```

**Circuit Breaker Status**:
```json
{
  "circuit_breaker": {
    "service": "pes_referral_processing",
    "status": "open",
    "failure_count": 5,
    "last_failure": "2025-09-15T14:25:00.000Z",
    "next_retry_allowed": "2025-09-15T14:35:00.000Z",
    "estimated_recovery_time": "10_minutes"
  }
}
```

---

## Internationalization and Localization

### Multi-Language Support

**Language Negotiation**:
```http
Accept-Language: es-CL,en-US;q=0.8,pt-BR;q=0.6
Content-Language: es-CL
```

**Localized Message Structure**:
```json
{
  "message_content": {
    "default_language": "en",
    "localizations": {
      "es-CL": {
        "referral_reason": "activación_beneficio_desempleo",
        "status_message": "Derivación creada exitosamente y asignada a trabajador social"
      },
      "pt-BR": {
        "referral_reason": "ativação_benefício_desemprego",
        "status_message": "Encaminhamento criado com sucesso e atribuído ao assistente social"
      }
    }
  }
}
```

### Cultural Adaptation

**Name Formatting**:
```json
{
  "name_formatting": {
    "culture": "es-CL",
    "pattern": "{given_name} {paternal_surname} {maternal_surname}",
    "display_name": "María González Silva",
    "sorting_name": "González Silva, María"
  }
}
```

**Date and Currency Formatting**:
```json
{
  "formatting": {
    "locale": "es-CL",
    "date_format": "dd/MM/yyyy",
    "currency_format": "$#,##0 CLP",
    "number_format": "#.##0,00"
  }
}
```

---

## Performance Optimization

### Message Compression

**Compression Support**:
```http
Content-Encoding: gzip
Accept-Encoding: gzip, deflate, br
```

**Selective Field Loading**:
```json
{
  "field_selection": {
    "include_fields": [
      "person.identifiers",
      "person.name",
      "employment_status.current_status",
      "employment_details.compensation.base_salary"
    ],
    "exclude_fields": [
      "person.contact_info.address.detailed_address",
      "employment_details.full_employment_history"
    ]
  }
}
```

### Caching Strategies

**Cache Control Headers**:
```http
Cache-Control: private, max-age=3600
ETag: "employment-status-v1-20250915-1630"
Last-Modified: Tue, 15 Sep 2025 16:30:00 GMT
```

**Conditional Requests**:
```http
If-None-Match: "employment-status-v1-20250915-1630"
If-Modified-Since: Tue, 15 Sep 2025 16:30:00 GMT
```

---

## Security Considerations

### Message-Level Security

**Digital Signatures**:
```json
{
  "signature": {
    "algorithm": "RS256",
    "signature_value": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "signing_certificate": "-----BEGIN CERTIFICATE-----\n...",
    "timestamp": "2025-09-15T14:30:00.000Z"
  }
}
```

**Field-Level Encryption**:
```json
{
  "person": {
    "identifiers": [
      {
        "identifier_type": "national_id",
        "identifier_value": "encrypted:AES256:base64encodedvalue",
        "encryption_key_id": "employment-pii-key-2024"
      }
    ]
  }
}
```

### Data Minimization

**Privacy Filters**:
```json
{
  "privacy_context": {
    "requesting_system_role": "sp_compliance_officer",
    "purpose_of_use": "benefit_eligibility_verification",
    "data_minimization_applied": true,
    "filtered_fields": [
      "person.contact_info.detailed_address",
      "employment_details.salary_history",
      "medical_restrictions.specific_conditions"
    ]
  }
}
```

---

## Open Issues

`TODO(schema)`: Generate formal JSON Schema definitions for all employment message structures with comprehensive validation rules.

`TODO(examples)`: Create country-specific message examples demonstrating real-world integration patterns.

`TODO(tooling)`: Develop message validation and testing tools for employment-specific API implementations.

`TODO(documentation)`: Create interactive documentation with message builder tools for integration developers.