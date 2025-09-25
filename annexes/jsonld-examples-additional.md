# Complete JSON-LD Data Object Examples

This section provides comprehensive, well-annotated JSON-LD examples for all Employment-SP data objects following DCI standards.

## **DO.EMPL.01 - Employment Referral (DCI-Compliant)**

Complete three-part DCI message structure for employment referrals:

```json
{
  "signature": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJzcC1taXMtY2wiLCJpYXQiOjE2OTQ3ODQwMDAsImV4cCI6MTY5NDc4NzYwMH0.signature_bytes_base64",
  "header": {
    "version": "1.0.0",
    "message_id": "550e8400-e29b-41d4-a716-446655440000",
    "message_ts": "2025-09-15T14:30:00.000Z",
    "action": "referral",
    "sender_id": "SP-MIS-CL",
    "receiver_id": "PES-CL",
    "sender_uri": "https://api-cl.sp-mis.gov/v1",
    "total_count": 1,
    "is_msg_encrypted": true
    "meta": {
      "employment_context": {
        "purpose_of_use": "employment_service_provision",
        "legal_basis": "social_protection_law_2023_article_15",
        "data_minimization_level": "service_necessary"
      }
    }
  },
  "message": {
    "transaction_id": "550e8400-e29b-41d4-a716-446655440001",
    "referral_data": {
      "@context": {
        "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
        "social": "https://schema.spdci.org/extensions/social/v1/",
        "empl": "https://schema.spdci.org/extensions/employment/v1/",
        "common": "https://schema.spdci.org/common/v1/"
      },
      "@id": "empl:EmploymentReferral_12345",
      "@type": "empl:EmploymentReferral",

      "assistance_unit": {
        "@type": "spdci:AssistanceUnit",
        "person_reference": "SP_REF_12345"
      },

      "programme_identifier": {
        "@type": "spdci:Programme",
        "programme_identifier": "IBR-CASH-2024",
        "programme_name": "Ingreso Familiar de Emergencia",
        "implementing_institution": "MIDESO",
        "institution_type": "government_ministry",
        "social_protection_functions": ["cash_transfers"]
      },

      "enrollment_date": "2024-01-15T00:00:00Z",
      "enrollment_status": "active",

      "benefits": [
        {
          "@type": "spdci:Benefit",
          "benefit_type": "cash_transfer",
          "benefit_amount": {
            "currency": "CLP",
            "amount": 180000
          },
          "benefit_frequency": "monthly"
        }
      ],

      "empl:referral_reason": "unemployment_activation",
      "empl:target_pes_jurisdiction": "REGION_METROPOLITANA",
      "empl:compliance_requirements": {
        "job_search_obligation": true,
        "reporting_frequency": "monthly",
        "training_participation": false,
        "geographic_mobility": true
      },

      "consent": {
        "consent_given": true,
        "consent_timestamp": "2025-09-15T14:00:00.000Z",
        "data_retention_period": "P2Y"
        "consent_type": "employment_services",
        "legal_basis": "social_protection_law_2023"
      }
    },
    "locale": "es-CL"
  }
}
```

**Developer Notes for DO.EMPL.01:**
- **DCI Compliance**: Uses three-part structure (signature + header + message)
- **IBR Integration**: Extends existing `spdci:Beneficiary` and `spdci:Programme` objects
- **Data Minimization**: Only includes `person_reference`, not full PII
- **Semantic Linking**: Uses JSON-LD to link to existing DCI schemas
- **Country Extensions**: Supports country-specific extensions in `meta` section

## **DO.EMPL.02 - Employment Status Verification**

Real-time employment verification with multiple data sources:

```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
    "empl": "https://schema.spdci.org/extensions/employment/v1/",
    "common": "https://schema.spdci.org/common/v1/"
  },
  "@id": "empl:EmploymentVerification_67890",
  "@type": "empl:EmploymentVerification",

  "verification_request": {
    "person_reference": "SP_REF_12345",
    "verification_date": "2025-09-15",
    "verification_sources": [
      "payroll_system",
      "tax_authority",
      "social_security",
      "employer_declaration"
    ]
  },

  "employment_status": {
    "current_status": "employed",
    "status_date": "2025-08-01",
    "verification_date": "2025-09-15T15:00:00.000Z",
    "confidence_level": "high",
    "data_quality_score": 0.95

    "employment_details": {
      "employer_tax_id": "76123456-7",
      "employer_sector": "information_technology",
      "employment_type": "permanent_full_time",
      "monthly_income_band": "450000-500000_CLP"
      "employment_start_date": "2025-08-01",
      "contract_type": "indefinite_term"
    },

    "verification_metadata": {
      "sources_confirmed": 3,
      "sources_checked": 4
      "last_payroll_date": "2025-08-31",
      "last_contribution_date": "2025-08-31",
      "verification_method": "automated_cross_check"
    }
  },

  "sp_benefit_impact": {
    "benefit_continuation_eligible": false,
    "graduation_triggered": true,
    "income_supplement_needed": false
    "reporting_requirements": {
      "next_report_due": "2025-10-15",
      "reporting_frequency": "monthly",
      "income_declaration_required": true
    }
  }
}
```

**Developer Notes for DO.EMPL.02:**
- **Real-time Verification**: Checks multiple authoritative data sources
- **Privacy Protection**: Uses income bands instead of exact amounts
- **Benefit Integration**: Automatically calculates impact on SP benefits
- **Quality Scoring**: Provides confidence metrics for decision-making

## **DO.EMPL.03 - Employment Benefit (Training & Support)**

DCI-compliant employment benefit coordination with training providers:

```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
    "empl": "https://schema.spdci.org/extensions/employment/v1/",
    "common": "https://schema.spdci.org/common/v1/"
  },
  "@id": "empl:EmploymentBenefit_11111",
  "@type": "empl:EmploymentBenefit",

  "beneficiary_reference": {
    "@type": "spdci:Beneficiary",
    "person_reference": "SP_REF_12345",
    "programme_identifier": "IBR-CASH-2024"
  },

  "employment_benefit": {
    "benefit_type": "training_voucher",
    "benefit_identifier": "VOUCHER-2025-001234",
    "benefit_value": {
      "currency": "CLP",
      "amount": 300000
      "payment_schedule": "upon_completion"
    },

    "training_details": {
      "training_provider": {
        "provider_id": "CERT-PROV-456",
        "provider_name": "Technical Skills Institute",
        "certification_type": "government_certified",
        "quality_rating": 4.2
      },
      "course_details": {
        "course_id": "TECH-SUPPORT-101",
        "course_name": "IT Technical Support Certification",
        "duration_weeks": 12,
        "schedule": "part_time_evening",
        "delivery_mode": "hybrid"
        "start_date": "2025-10-01",
        "completion_date": "2025-12-20"
      },
      "learning_outcomes": [
        "computer_hardware_troubleshooting",
        "network_configuration",
        "customer_service_skills",
        "professional_certification"
      ]
    },

    "eligibility_criteria": {
      "work_capacity_confirmed": true,
      "prior_training_gap": 24
      "employment_commitment": "job_search_upon_completion",
      "geographic_mobility": "willing_to_relocate"
    },

    "compliance_requirements": {
      "attendance_minimum": 0.8
      "progress_reporting": "weekly",
      "completion_required": true,
      "post_training_job_search": {
        "duration_months": 6,
        "minimum_applications": 4,
        "reporting_frequency": "bi_weekly"
      }
    }
  },

  "benefit_coordination": {
    "existing_benefits_maintained": true
    "additional_support": {
      "transport_allowance": {
        "currency": "CLP",
        "amount": 15000,
        "frequency": "monthly"
      },
      "childcare_support": {
        "available": true,
        "provider": "municipality_daycare"
      }
    },
    "transition_plan": {
      "expected_employment_outcome": "full_time_employment",
      "target_salary_range": "500000-700000_CLP",
      "benefit_graduation_timeline": "P6M"
    }
  }
}
```

**Developer Notes for DO.EMPL.03:**
- **Benefit Coordination**: Integrates with existing SP benefits seamlessly
- **Provider Integration**: Links to certified training provider networks
- **Outcome Tracking**: Defines clear success metrics and timelines
- **Comprehensive Support**: Includes wraparound services (transport, childcare)

## **DO.EMPL.04 - Job Placement (Successful Outcome)**

Complete job placement record with outcome tracking:

```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
    "empl": "https://schema.spdci.org/extensions/employment/v1/",
    "common": "https://schema.spdci.org/common/v1/"
  },
  "@id": "empl:JobPlacement_22222",
  "@type": "empl:JobPlacement",

  "placement_details": {
    "placement_id": "PLACEMENT-2025-5678",
    "referral_source": "empl:EmploymentReferral_12345",
    "placement_date": "2025-12-15",
    "placement_type": "direct_hire"

    "job_details": {
      "employer": {
        "employer_id": "EMP-RETAIL-789",
        "employer_name": "Tech Solutions SPA"
        "employer_sector": "information_technology",
        "employer_size": "medium_enterprise"
        "location": {
          "region": "REGION_METROPOLITANA",
          "commune": "SANTIAGO",
          "transport_accessibility": "high"
        }
      },

      "position_details": {
        "job_title": "IT Support Specialist",
        "job_function": "technical_support",
        "employment_type": "permanent_full_time",
        "contract_duration": "indefinite",
        "salary": {
          "currency": "CLP",
          "monthly_amount": 550000,
          "includes_benefits": true,
          "benefit_package": ["health_insurance", "transport_allowance", "performance_bonus"]
        },
        "work_schedule": {
          "hours_per_week": 40,
          "shift_pattern": "day_shift",
          "flexibility": "hybrid_remote_option"
        }
      }
    },

    "placement_pathway": {
      "referral_to_interview": "P5D",
      "interview_to_offer": "P3D",
      "offer_to_start": "P14D",
      "total_placement_time": "P22D"

      "supporting_services": [
        "skills_assessment",
        "interview_preparation",
        "cv_optimization",
        "workplace_orientation"
      ],

      "training_completed": {
        "course_reference": "TECH-SUPPORT-101"
        "completion_date": "2025-12-20",
        "final_grade": "distinction",
        "certification_obtained": "IT_Support_Professional"
      }
    }
  },

  "outcome_tracking": {
    "employment_sustainability": {
      "30_day_retention": null
      "90_day_retention": null,
      "180_day_retention": null,
      "365_day_retention": null
    },

    "progression_indicators": {
      "salary_progression": {
        "baseline_salary": 550000,
        "target_6_month": 600000,
        "target_12_month": 650000
      },
      "skills_development": {
        "additional_certifications_planned": 2,
        "career_advancement_pathway": "senior_technician"
      }
    },

    "benefit_graduation": {
      "cash_transfer_ended": "2025-12-15",
      "transition_support_period": "P3M"
      "emergency_support_available": true,
      "re_entry_criteria": "job_loss_within_6_months"
    }
  },

  "program_impact": {
    "cost_per_placement": {
      "currency": "CLP",
      "total_cost": 480000
      "breakdown": {
        "training_voucher": 300000,
        "case_management": 120000,
        "transport_support": 45000,
        "administrative": 15000
      }
    },
    "return_on_investment": {
      "estimated_tax_revenue_12_months": 660000,
      "benefit_savings_12_months": 2160000,
      "net_fiscal_impact": 2340000
    }
  }
}
```

**Developer Notes for DO.EMPL.04:**
- **Outcome Measurement**: Comprehensive tracking for program evaluation
- **ROI Calculation**: Detailed cost-benefit analysis for policy makers
- **Sustainability Metrics**: Long-term employment retention tracking
- **Lifecycle Linking**: Connects to original referral and training records

## **DO.EMPL.05 - Compliance Monitoring (Ongoing Tracking)**

Real-time compliance monitoring with automated alerts:

```json
{
  "@context": {
    "spdci": "https://schema.spdci.org/extensions/ibr/v1/",
    "empl": "https://schema.spdci.org/extensions/employment/v1/",
    "common": "https://schema.spdci.org/common/v1/"
  },
  "@id": "empl:ComplianceMonitoring_33333",
  "@type": "empl:ComplianceMonitoring",

  "monitoring_subject": {
    "person_reference": "SP_REF_12345",
    "active_programme": "IBR-CASH-2024",
    "monitoring_period": {
      "start_date": "2025-09-15",
      "end_date": "2026-03-15",                             // 6-month monitoring period
      "monitoring_frequency": "weekly"
    }
  },

  "compliance_status": {
    "overall_status": "compliant"
    "last_assessment_date": "2025-09-15T16:00:00.000Z",
    "next_assessment_due": "2025-09-22T16:00:00.000Z",

    "employment_obligations": {
      "job_search_requirement": {
        "status": "met",
        "applications_this_month": 6
        "minimum_required": 4,
        "documentation_provided": true,
        "last_application_date": "2025-09-12"
      },

      "pes_appointments": {
        "status": "compliant",
        "last_appointment": "2025-09-08T10:00:00.000Z",
        "next_appointment": "2025-10-08T10:00:00.000Z",
        "missed_appointments": 0,
        "total_appointments": 4
      },

      "training_participation": {
        "status": "enrolled",
        "course_reference": "TECH-SUPPORT-101",
        "attendance_rate": 0.95
        "minimum_required": 0.8,
        "progress_score": 0.87,
        "at_risk_indicators": []
      }
    },

    "income_reporting": {
      "status": "up_to_date",
      "last_report_date": "2025-09-01",
      "next_report_due": "2025-10-01",
      "reported_income_this_period": {
        "currency": "CLP",
        "amount": 0
        "source": "training_allowance_only"
      },
      "income_verification_status": "verified"
    }
  },

  "risk_assessment": {
    "risk_level": "low"
    "risk_factors": [],
    "protective_factors": [
      "high_training_engagement",
      "regular_pes_contact",
      "strong_motivation",
      "family_support_present"
    ],

    "early_warning_indicators": {
      "attendance_decline": false,
      "missed_appointments": false,
      "income_reporting_delays": false,
      "housing_instability": false,
      "health_issues": false
    }
  },

  "interventions_active": [
    {
      "intervention_type": "skills_training",
      "provider": "Technical Skills Institute",
      "start_date": "2025-10-01",
      "expected_completion": "2025-12-20",
      "success_indicators": ["course_completion", "certification_obtained"]
    },
    {
      "intervention_type": "case_management",
      "provider": "PES-SANTIAGO-CENTRO",
      "frequency": "weekly",
      "focus_areas": ["job_search_strategy", "interview_preparation"]
    }
  ],

  "automated_monitoring": {
    "data_sources": [
      "pes_case_management_system",
      "training_provider_portal",
      "employment_verification_service",
      "benefit_payment_system"
    ],
    "monitoring_rules": [
      {
        "rule_id": "ATTENDANCE_THRESHOLD",
        "condition": "training_attendance < 0.8",
        "action": "generate_alert",
        "alert_recipient": "case_manager"
      },
      {
        "rule_id": "MISSED_APPOINTMENT",
        "condition": "missed_appointments >= 2",
        "action": "escalate_to_supervisor",
        "grace_period": "P24H"
      },
      {
        "rule_id": "EMPLOYMENT_FOUND",
        "condition": "employment_status == 'employed'",
        "action": "trigger_benefit_review",
        "auto_processing": true
      }
    ]
  }
}
```

**Developer Notes for DO.EMPL.05:**
- **Automated Monitoring**: Real-time rule-based compliance checking
- **Risk Assessment**: Predictive indicators for early intervention
- **Multi-source Integration**: Aggregates data from multiple systems
- **Alert Management**: Configurable rules for case manager notifications

---

## **Implementation Guidelines for Developers**

### **JSON-LD Context Usage**
1. Always include the DCI-compliant `@context` with proper namespace definitions
2. Use `@id` and `@type` for semantic linking and type identification
3. Extend existing DCI schemas rather than creating new ones

### **Data Minimization Principles**
1. Use `person_reference` instead of full PII in cross-system exchanges
2. Employ income bands rather than exact amounts where possible
3. Include only data necessary for the specific use case

### **DCI Integration Patterns**
1. Follow three-part message structure (signature + header + message)
2. Use existing `spdci:Programme` and `spdci:Beneficiary` objects
3. Implement proper error handling with DCI error codes

### **Country-Specific Extensions**
All examples can be extended with country-specific namespaces:
- Chile: `cl-empl` namespace for RSH-SENCE integration
- South Korea: `kr-empl` namespace for Work24 platform
- Germany: `de-empl` namespace for SGB framework compliance
- Australia: `au-empl` namespace for JobActive provider network

### **Validation Requirements**
1. JSON-LD context must resolve to valid schema definitions
2. All required fields per DCI schemas must be present
3. Employment-specific business rules must be validated
4. Cross-system references must be verifiable

These examples provide a complete foundation for implementing Employment-SP interoperability while maintaining full DCI compliance and supporting real-world operational requirements.