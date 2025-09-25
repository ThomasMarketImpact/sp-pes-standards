# Employment-SP Data Types & Formats

This document defines employment-specific data types, validation rules, and formatting standards for Employment-Social Protection interoperability, extending DCI common data patterns.

## DTF.EMPL.01 — Employment Status Classification

**Description**: Standardized employment status categories supporting benefit eligibility determination and program targeting.

**Primary Employment Status**:
```json
{
  "employment_status": "employed",
  "employment_subtype": "formal_full_time",
  "verification_source": "employer_declaration",
  "verification_date": "2025-09-15",
  "confidence_level": "high"
}
```

**Enumerated Values**:
- **employed**: Currently in work arrangement with income generation
- **unemployed**: Without work, available for work, actively seeking employment
- **underemployed**: Working but seeking additional/different employment due to insufficient hours or income
- **inactive**: Not in labor force, not seeking employment
- **student**: Primarily engaged in education (may have part-time employment)
- **disabled**: Unable to work due to disability or health condition
- **retired**: Voluntarily withdrawn from labor force due to age or pension eligibility

**Subtype Specifications**:
```json
// Employment subtypes
"formal_full_time": "Full-time employment with social security contributions",
"formal_part_time": "Part-time employment with social security contributions",
"informal_employment": "Work without social security coverage or registration",
"self_employed_formal": "Self-employment with tax and social security registration",
"self_employed_informal": "Self-employment without formal registration",
"temporary_contract": "Fixed-term or seasonal employment arrangement",
"gig_economy": "Platform or task-based work arrangements",

// Unemployment subtypes
"job_loss_recent": "Became unemployed within last 6 months",
"long_term_unemployed": "Unemployed for 12+ months continuously",
"new_entrant": "First-time job seeker entering labor market",
"re_entrant": "Returning to labor force after absence",

// Inactive subtypes
"caregiver": "Not working due to care responsibilities",
"discouraged_worker": "Available but not seeking due to perceived lack of opportunities",
"seasonal_inactive": "Temporarily inactive due to seasonal work patterns"
```

**Validation Rules**:
- employment_status required for all employment-related transactions
- employment_subtype must be compatible with primary status
- verification_date must be within 30 days for high-confidence determinations
- confidence_level must reflect verification source reliability

**Business Logic**:
- Status changes trigger automatic benefit eligibility reassessment
- Long-term unemployment status activates enhanced support services
- Formal employment triggers social security compliance verification

---

## DTF.EMPL.02 — Skills and Qualifications Framework

**Description**: Standardized representation of individual skills, qualifications, and competencies for employment matching and training needs assessment.

**Skills Classification Structure**:
```json
{
  "person_skills": {
    "basic_skills": [
      {
        "skill_code": "BASIC_001",
        "skill_name": "literacy",
        "proficiency_level": "intermediate",
        "assessment_method": "self_reported",
        "assessment_date": "2025-09-15"
      }
    ],
    "technical_skills": [
      {
        "skill_code": "TECH_125",
        "skill_name": "computer_programming_python",
        "proficiency_level": "advanced",
        "assessment_method": "certification",
        "certification_authority": "Python Institute",
        "certification_expiry": "2026-09-15"
      }
    ],
    "soft_skills": [
      {
        "skill_code": "SOFT_043",
        "skill_name": "customer_service",
        "proficiency_level": "expert",
        "work_experience_years": 5
      }
    ]
  }
}
```

**Proficiency Levels**:
- **beginner**: Basic understanding, requires supervision and training
- **intermediate**: Competent performance with occasional guidance
- **advanced**: Independent performance with high quality output
- **expert**: Mastery level, able to train others and handle complex situations

**Assessment Methods**:
- **self_reported**: Individual self-assessment (lowest validation confidence)
- **supervisor_evaluation**: Workplace performance assessment
- **formal_testing**: Standardized competency examination
- **certification**: Third-party professional certification
- **educational_credential**: Academic qualification or training completion
- **work_portfolio**: Demonstrated performance through work samples

**Educational Qualifications Structure**:
```json
{
  "qualifications": [
    {
      "qualification_id": "QUAL_EDU_001",
      "qualification_type": "academic_degree",
      "qualification_level": "bachelor_degree",
      "field_of_study": "computer_science",
      "institution_name": "University of Technology",
      "completion_date": "2020-06-30",
      "recognition_status": "national_recognized",
      "equivalency_assessment": {
        "assessed": true,
        "equivalent_local_qualification": "engineering_degree_chile",
        "assessment_authority": "Chilean Education Ministry"
      }
    }
  ]
}
```

**International Skills Classification Mapping**:
- **ISCO-08 Integration**: Map technical skills to International Standard Classification of Occupations
- **ESCO Alignment**: European Skills, Competences, Qualifications framework compatibility
- **National Framework**: Country-specific skills classification system integration
- **Industry Standards**: Sector-specific competency frameworks (IT, healthcare, construction, etc.)

---

## DTF.EMPL.03 — Employment History and Experience

**Description**: Comprehensive employment history tracking for benefit eligibility, skills verification, and career development planning.

**Employment Record Structure**:
```json
{
  "employment_history": [
    {
      "employment_record_id": "EMP_2024_001",
      "employer": {
        "employer_name": "Tech Solutions SPA",
        "employer_tax_id": "96.123.456-7",
        "sector": "information_technology",
        "company_size": "medium_enterprise"
      },
      "position_details": {
        "job_title": "Software Developer",
        "occupation_code": "ISCO_2512",
        "employment_type": "permanent_full_time",
        "seniority_level": "mid_level",
        "supervisory_responsibilities": false
      },
      "employment_period": {
        "start_date": "2022-03-01",
        "end_date": "2024-08-31",
        "still_employed": false,
        "duration_months": 30
      },
      "compensation": {
        "base_salary": {
          "amount": "850000",
          "currency_code": "CLP",
          "frequency": "monthly"
        },
        "variable_compensation": {
          "amount": "100000",
          "currency_code": "CLP",
          "type": "performance_bonus"
        },
        "benefits": ["health_insurance", "pension_contributions", "meal_vouchers"]
      },
      "separation_details": {
        "separation_reason": "voluntary_resignation",
        "eligible_for_unemployment_benefits": true,
        "notice_period_days": 30,
        "severance_paid": false
      },
      "verification": {
        "verification_method": "employer_confirmation",
        "verification_date": "2024-09-01",
        "verified_by": "labor_directorate_system"
      }
    }
  ]
}
```

**Separation Reason Classifications**:
- **voluntary_resignation**: Employee-initiated termination
- **mutual_agreement**: Agreed termination between parties
- **redundancy**: Position elimination due to business needs
- **performance_dismissal**: Termination due to inadequate performance
- **misconduct_dismissal**: Termination due to disciplinary reasons
- **contract_expiry**: Natural end of fixed-term contract
- **business_closure**: Employer ceased operations
- **health_reasons**: Termination due to health inability to work

**Work Experience Aggregation**:
```json
{
  "experience_summary": {
    "total_work_months": 84,
    "sectors_worked": ["information_technology", "retail", "education"],
    "primary_occupation": "software_developer",
    "management_experience": false,
    "international_experience": true,
    "unemployment_periods": [
      {
        "start_date": "2021-06-01",
        "end_date": "2022-02-28",
        "duration_months": 9,
        "reason": "job_search_after_studies"
      }
    ]
  }
}
```

---

## DTF.EMPL.04 — Training and Development Records

**Description**: Structured tracking of training programs, professional development, and capacity building interventions.

**Training Record Structure**:
```json
{
  "training_participation": {
    "training_record_id": "TRN_2025_045",
    "program_details": {
      "program_name": "Digital Marketing Fundamentals",
      "program_type": "vocational_skills_training",
      "training_provider": "Professional Institute ABC",
      "provider_accreditation": "ministry_of_education_accredited",
      "program_duration_hours": 160,
      "delivery_mode": "hybrid_online_classroom"
    },
    "participation_period": {
      "enrollment_date": "2025-10-01",
      "start_date": "2025-10-15",
      "completion_date": "2025-12-20",
      "status": "in_progress"
    },
    "curriculum_content": {
      "core_modules": [
        "social_media_marketing",
        "content_creation",
        "analytics_measurement",
        "advertising_platforms"
      ],
      "practical_components": ["portfolio_development", "client_project"],
      "assessment_methods": ["written_exam", "practical_assessment", "portfolio_submission"]
    },
    "performance_tracking": {
      "attendance_rate": 0.92,
      "module_completion_rate": 0.75,
      "current_grade": "B+",
      "performance_trend": "improving"
    },
    "certification_outcome": {
      "certification_awarded": null,
      "expected_certification": "digital_marketing_specialist",
      "industry_recognition": true,
      "skills_validated": ["social_media_management", "digital_analytics", "campaign_optimization"]
    },
    "employment_connection": {
      "job_placement_support": true,
      "placement_target_date": "2026-01-31",
      "employer_partnerships": ["Marketing Agency Network", "E-commerce Retailers Association"],
      "expected_salary_range": {
        "min_amount": "450000",
        "max_amount": "650000",
        "currency_code": "CLP"
      }
    }
  }
}
```

**Training Program Types**:
- **vocational_skills_training**: Occupation-specific skill development
- **basic_education**: Literacy, numeracy, and foundational skills
- **digital_literacy**: Computer and internet usage skills
- **entrepreneurship**: Business development and self-employment skills
- **life_skills**: Soft skills and workplace readiness
- **language_training**: Language proficiency for employment
- **apprenticeship**: Work-based learning with employer partnership
- **university_extension**: Higher education continuing education programs

**Completion Status Classifications**:
- **enrolled**: Currently participating in active program
- **in_progress**: Attending classes but not yet completed
- **completed_successful**: Successfully finished with passing grade
- **completed_unsuccessful**: Finished but did not meet completion criteria
- **withdrawn_voluntary**: Student-initiated early departure
- **withdrawn_involuntary**: Removed due to non-compliance or performance
- **on_hold**: Temporarily suspended with intent to resume

---

## DTF.EMPL.05 — Job Vacancy and Labor Demand

**Description**: Standardized job posting and labor market demand information for employment matching and planning.

**Job Vacancy Structure**:
```json
{
  "job_vacancy": {
    "vacancy_id": "JOB_2025_12345",
    "employer_information": {
      "employer_name": "Local Manufacturing Corp",
      "employer_tax_id": "76.987.654-3",
      "sector": "manufacturing",
      "company_size": "large_enterprise",
      "workplace_location": {
        "address_line_1": "Industrial Park Avenue 100",
        "locality": "Santiago",
        "region_code": "RM",
        "country_code": "CL"
      }
    },
    "position_details": {
      "job_title": "Production Line Supervisor",
      "occupation_code": "ISCO_3122",
      "department": "production_operations",
      "reports_to": "production_manager",
      "employment_type": "permanent_full_time",
      "shift_schedule": "rotating_shifts",
      "travel_requirements": "none"
    },
    "requirements": {
      "education_minimum": "secondary_technical",
      "experience_minimum_years": 3,
      "required_skills": ["production_management", "quality_control", "team_leadership"],
      "preferred_skills": ["lean_manufacturing", "safety_management", "spanish_fluent"],
      "physical_requirements": ["standing_prolonged", "lifting_moderate"],
      "special_requirements": ["clean_background_check", "health_certificate"]
    },
    "compensation_package": {
      "salary_range": {
        "min_amount": "650000",
        "max_amount": "800000",
        "currency_code": "CLP",
        "frequency": "monthly"
      },
      "variable_pay": {
        "performance_bonus_potential": "up_to_15_percent",
        "overtime_available": true,
        "shift_differential": "10_percent_night_shift"
      },
      "benefits": [
        "health_insurance_full",
        "pension_contributions_employer_match",
        "meal_allowance",
        "transportation_subsidy",
        "annual_vacation_21_days"
      ]
    },
    "application_process": {
      "application_deadline": "2025-11-15",
      "application_method": "online_pes_platform",
      "contact_person": "HR Department",
      "selection_process": ["application_review", "technical_interview", "practical_assessment"],
      "expected_start_date": "2025-12-01",
      "positions_available": 2
    },
    "accessibility_accommodations": {
      "wheelchair_accessible": true,
      "vision_impairment_accommodations": false,
      "hearing_impairment_accommodations": true,
      "flexible_schedule_available": false
    }
  }
}
```

**Labor Market Intelligence**:
```json
{
  "demand_analysis": {
    "occupation_code": "ISCO_3122",
    "regional_demand": {
      "current_vacancies": 45,
      "monthly_average_vacancies": 38,
      "demand_trend": "increasing",
      "seasonal_variation": "stable"
    },
    "skill_gaps": [
      {
        "skill_name": "digital_production_systems",
        "gap_severity": "high",
        "training_availability": "limited"
      }
    ],
    "salary_benchmarking": {
      "regional_median": "720000",
      "entry_level_range": "550000-650000",
      "experienced_range": "750000-950000",
      "currency_code": "CLP"
    }
  }
}
```

---

## DTF.EMPL.06 — Benefit Payment and Financial Support

**Description**: Employment-related financial support tracking including unemployment benefits, training stipends, and employment subsidies.

**Payment Record Structure**:
```json
{
  "employment_benefit_payment": {
    "payment_id": "PAY_EMPL_2025_78901",
    "beneficiary_id": "person:12345",
    "benefit_program": {
      "program_code": "UNEMPLOYMENT_INSURANCE",
      "program_name": "Employment Insurance Benefit",
      "administering_agency": "Ministry of Labor"
    },
    "benefit_calculation": {
      "base_calculation_method": "percentage_previous_wage",
      "calculation_percentage": 0.60,
      "reference_wage": {
        "amount": "750000",
        "currency_code": "CLP",
        "reference_period": "last_6_months_average"
      },
      "calculated_benefit": {
        "amount": "450000",
        "currency_code": "CLP",
        "frequency": "monthly"
      }
    },
    "payment_details": {
      "payment_date": "2025-09-30",
      "payment_amount": {
        "gross_amount": "450000",
        "tax_withholding": "0",
        "net_amount": "450000",
        "currency_code": "CLP"
      },
      "payment_method": "bank_transfer",
      "bank_account": {
        "account_type": "checking",
        "bank_code": "001",
        "account_number_masked": "****1234"
      }
    },
    "payment_period": {
      "benefit_month": "2025-09",
      "coverage_start_date": "2025-09-01",
      "coverage_end_date": "2025-09-30",
      "cumulative_payments_count": 3,
      "remaining_entitlement_months": 9
    },
    "compliance_verification": {
      "job_search_activities": {
        "required_weekly_activities": 4,
        "reported_activities": 5,
        "compliance_status": "compliant"
      },
      "availability_confirmation": {
        "available_for_work": true,
        "availability_restrictions": ["part_time_only"],
        "last_confirmed_date": "2025-09-25"
      },
      "training_participation": {
        "enrolled_in_training": true,
        "training_program": "Digital Skills Development",
        "attendance_rate": 0.95
      }
    }
  }
}
```

**Employment Subsidy Structure**:
```json
{
  "employment_subsidy": {
    "subsidy_id": "SUBS_YOUTH_2025_456",
    "subsidy_type": "youth_employment_incentive",
    "beneficiary_type": "employee",
    "eligibility_criteria": {
      "age_range": {"min": 18, "max": 24},
      "income_threshold": {
        "amount": "662348",
        "currency_code": "CLP",
        "threshold_type": "monthly_gross"
      },
      "vulnerability_status": "social_registry_eligible",
      "employment_type_required": "formal_employment"
    },
    "subsidy_calculation": {
      "calculation_base": "registered_wage",
      "subsidy_percentage": 0.30,
      "maximum_subsidy": {
        "amount": "200000",
        "currency_code": "CLP"
      },
      "duration_months": 24
    },
    "employer_component": {
      "employer_eligible": true,
      "employer_subsidy_rate": 0.10,
      "employer_requirements": ["timely_social_security_payments", "employment_contract_compliance"]
    }
  }
}
```

---

## DTF.EMPL.07 — Compliance and Monitoring Data

**Description**: Structured tracking of compliance obligations, monitoring activities, and enforcement actions for employment-related benefits.

**Compliance Monitoring Structure**:
```json
{
  "compliance_record": {
    "monitoring_id": "COMP_2025_M789",
    "person_id": "person:12345",
    "monitoring_period": {
      "start_date": "2025-09-01",
      "end_date": "2025-11-30",
      "review_frequency": "monthly"
    },
    "obligation_tracking": [
      {
        "obligation_code": "JOB_SEARCH_REQ",
        "obligation_description": "Maintain active job search with minimum 4 applications per month",
        "measurement_criteria": {
          "metric_type": "application_count",
          "required_minimum": 4,
          "measurement_period": "monthly"
        },
        "compliance_status": "compliant",
        "current_performance": {
          "reported_value": 6,
          "verification_method": "system_tracked",
          "last_update_date": "2025-09-30"
        }
      },
      {
        "obligation_code": "PES_APPOINTMENT",
        "obligation_description": "Attend scheduled PES caseworker appointments",
        "measurement_criteria": {
          "metric_type": "attendance_rate",
          "required_minimum": 1.0,
          "measurement_period": "per_appointment"
        },
        "compliance_status": "non_compliant",
        "current_performance": {
          "reported_value": 0.0,
          "missed_appointments": 1,
          "last_missed_date": "2025-09-20",
          "excuse_provided": false
        }
      }
    ],
    "overall_assessment": {
      "compliance_score": 0.75,
      "compliance_category": "partial_compliance",
      "risk_level": "medium",
      "intervention_required": true
    }
  }
}
```

**Enforcement Action Structure**:
```json
{
  "enforcement_action": {
    "action_id": "ENF_2025_A234",
    "person_id": "person:12345",
    "violation_details": {
      "violation_type": "missed_appointment",
      "violation_date": "2025-09-20",
      "severity_level": "minor",
      "repeat_violation": false
    },
    "action_taken": {
      "action_type": "formal_warning",
      "action_date": "2025-09-25",
      "action_authority": "pes_compliance_officer",
      "warning_details": {
        "warning_letter_sent": true,
        "warning_content": "Appointment attendance is mandatory condition for benefit continuation",
        "cure_period_days": 30,
        "next_violation_consequence": "benefit_suspension"
      }
    },
    "follow_up_requirements": {
      "compliance_review_date": "2025-10-25",
      "mandatory_meeting_scheduled": true,
      "additional_monitoring": false
    }
  }
}
```

---

## DTF.EMPL.08 — Performance and Outcome Metrics

**Description**: Standardized metrics for measuring employment program effectiveness and individual outcome tracking.

**Individual Outcome Tracking**:
```json
{
  "outcome_measurement": {
    "person_id": "person:12345",
    "program_enrollment": {
      "program_code": "ACTIVE_LABOR_MARKET_PROGRAM",
      "enrollment_date": "2025-01-15",
      "program_completion_date": "2025-09-30",
      "completion_status": "successful"
    },
    "employment_outcomes": {
      "job_placement_achieved": true,
      "placement_date": "2025-10-15",
      "time_to_placement_days": 15,
      "placement_quality": {
        "employment_type": "permanent_full_time",
        "wage_level": {
          "amount": "520000",
          "currency_code": "CLP",
          "comparison_to_previous": "increase_15_percent"
        },
        "skill_match": "high_match",
        "career_progression_potential": "good"
      }
    },
    "sustainability_tracking": {
      "employment_retention_3_months": null,
      "employment_retention_6_months": null,
      "employment_retention_12_months": null,
      "wage_progression": null,
      "job_satisfaction_score": null
    },
    "benefit_outcomes": {
      "benefit_graduation": true,
      "benefit_graduation_date": "2025-11-01",
      "graduation_reason": "income_above_threshold",
      "support_services_continued": ["career_counseling"]
    }
  }
}
```

**Program Performance Metrics**:
```json
{
  "program_performance": {
    "program_code": "YOUTH_EMPLOYMENT_PROGRAM",
    "reporting_period": {
      "start_date": "2025-01-01",
      "end_date": "2025-09-30"
    },
    "participation_metrics": {
      "total_enrollments": 1250,
      "active_participants": 890,
      "completion_rate": 0.78,
      "dropout_rate": 0.22
    },
    "employment_outcomes": {
      "job_placement_rate": 0.65,
      "average_time_to_placement_days": 45,
      "employment_retention_6_months": 0.82,
      "wage_improvement_average_percent": 0.28
    },
    "cost_effectiveness": {
      "cost_per_participant": {
        "amount": "450000",
        "currency_code": "CLP"
      },
      "cost_per_placement": {
        "amount": "692000",
        "currency_code": "CLP"
      },
      "return_on_investment": {
        "roi_ratio": 2.4,
        "payback_period_months": 18
      }
    }
  }
}
```

---

## DTF.EMPL.09 — Geographic and Administrative Classifications

**Description**: Location-based data standards for service delivery, labor market analysis, and administrative coordination.

**Geographic Classification**:
```json
{
  "geographic_data": {
    "administrative_division": {
      "country_code": "CL",
      "region_code": "RM",
      "region_name": "Metropolitan Region",
      "province_code": "SANTIAGO",
      "commune_code": "PROVIDENCIA",
      "commune_name": "Providencia"
    },
    "labor_market_area": {
      "lma_code": "SANTIAGO_CENTRAL",
      "lma_name": "Santiago Central Labor Market Area",
      "economic_characteristics": ["services_dominant", "high_skilled_jobs", "tourism_sector"],
      "unemployment_rate": 0.078,
      "labor_force_participation": 0.64
    },
    "service_delivery_area": {
      "pes_office_code": "PES_SANTIAGO_CENTRO",
      "pes_office_name": "Santiago Centro Employment Office",
      "catchment_area": ["providencia", "las_condes", "vitacura"],
      "service_capacity": {
        "max_active_cases": 2500,
        "current_caseload": 1980,
        "capacity_utilization": 0.79
      }
    }
  }
}
```

**Administrative Entity Classifications**:
```json
{
  "administrative_entities": {
    "implementing_agencies": [
      {
        "agency_code": "SENCE",
        "agency_name": "National Training and Employment Service",
        "agency_type": "employment_services",
        "parent_ministry": "Ministry of Labor and Social Security",
        "functions": ["training_programs", "employment_subsidies", "labor_market_information"]
      },
      {
        "agency_code": "IPS",
        "agency_name": "Social Security Institute",
        "agency_type": "social_protection",
        "parent_ministry": "Ministry of Labor and Social Security",
        "functions": ["pension_administration", "family_benefits", "unemployment_insurance"]
      }
    ],
    "data_sharing_agreements": [
      {
        "agreement_id": "DSA_SENCE_IPS_2025",
        "participating_agencies": ["SENCE", "IPS"],
        "data_categories": ["employment_status", "benefit_eligibility", "compliance_monitoring"],
        "sharing_frequency": "real_time",
        "legal_basis": "Employment_Social_Protection_Integration_Law_2024"
      }
    ]
  }
}
```

---

## DTF.EMPL.10 — Validation Rules and Business Logic

**Description**: Comprehensive validation rules, business logic constraints, and data integrity requirements for employment data.

**Field Validation Rules**:
```json
{
  "validation_rules": {
    "person_identifiers": {
      "national_id": {
        "pattern": "^[0-9]{8}-[0-9K]$",
        "checksum_validation": true,
        "uniqueness_required": true
      },
      "passport_number": {
        "pattern": "^[A-Z]{1,2}[0-9]{6,9}$",
        "expiry_date_required": true
      }
    },
    "employment_dates": {
      "start_date": {
        "format": "YYYY-MM-DD",
        "not_future_date": true,
        "minimum_age_check": 14
      },
      "end_date": {
        "format": "YYYY-MM-DD",
        "after_start_date": true,
        "not_future_date": true
      }
    },
    "monetary_amounts": {
      "salary": {
        "minimum_value": 0,
        "maximum_value": 50000000,
        "currency_required": true,
        "precision_digits": 0
      },
      "benefits": {
        "non_negative": true,
        "currency_match_national": true
      }
    }
  }
}
```

**Business Logic Constraints**:
```json
{
  "business_rules": {
    "benefit_eligibility": {
      "unemployment_benefit": {
        "minimum_contribution_months": 6,
        "maximum_benefit_duration_months": 12,
        "waiting_period_days": 0,
        "income_replacement_rate": 0.65,
        "maximum_benefit_amount": "unemployment_insurance_ceiling"
      },
      "employment_subsidy": {
        "age_restrictions": {"min": 18, "max": 29},
        "income_threshold_percentage": 0.4,
        "formal_employment_required": true,
        "maximum_subsidy_duration_months": 24
      }
    },
    "program_transitions": {
      "from_unemployment_to_employment": {
        "grace_period_days": 30,
        "benefit_tapering": true,
        "income_disregard_amount": "150000"
      },
      "training_to_employment": {
        "job_search_obligation_suspended": true,
        "training_stipend_continuation": true,
        "placement_support_required": true
      }
    },
    "compliance_monitoring": {
      "job_search_requirements": {
        "minimum_applications_monthly": 4,
        "evidence_documentation_required": true,
        "exemptions": ["training_participation", "health_restrictions"]
      },
      "appointment_obligations": {
        "maximum_missed_appointments": 2,
        "grace_period_first_miss": 7,
        "escalation_second_miss": "formal_warning"
      }
    }
  }
}
```

**Data Quality Checks**:
```json
{
  "quality_assurance": {
    "completeness_rules": {
      "mandatory_fields": ["person_id", "employment_status", "verification_date"],
      "conditional_mandatory": {
        "if_employed": ["employer_name", "job_title", "start_date"],
        "if_training": ["program_name", "training_provider", "enrollment_date"]
      }
    },
    "consistency_checks": {
      "cross_system_validation": {
        "employment_status_vs_benefit_eligibility": true,
        "income_reporting_vs_tax_records": true,
        "training_enrollment_vs_provider_records": true
      },
      "temporal_consistency": {
        "employment_dates_sequential": true,
        "benefit_periods_non_overlapping": true,
        "training_completion_after_start": true
      }
    },
    "accuracy_indicators": {
      "verification_timeliness": "within_30_days",
      "source_authority_level": ["employer_direct", "government_system", "third_party_verified"],
      "confidence_scoring": {
        "high": "government_verified",
        "medium": "employer_reported",
        "low": "self_reported"
      }
    }
  }
}
```

---

## Open Issues

`TODO(standardization)`: Align occupation codes with national classification system and ISCO-08 international standard.

`TODO(integration)`: Define JSON Schema definitions for all data types with validation rule enforcement.

`TODO(localization)`: Adapt currency, date, and address formats for implementation in different countries.

`TODO(performance)`: Establish caching strategies for frequently accessed reference data (skills, occupations, geographic codes).