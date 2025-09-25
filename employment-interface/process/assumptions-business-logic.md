# Employment-SP Process Standards: Assumptions & Business Logic

This document defines the business rules, validation logic, and specific assumptions governing the Employment-Social Protection interoperability interface.

## Overview

Business rules ensure consistent and compliant operation across SP and PES systems. These rules encompass eligibility criteria, validation logic, compliance requirements, and operational constraints that govern the five core employment processes.

---

## ASM.EMPL.01  Work Capacity and Referral Eligibility

**Scope**: Defines criteria for identifying work-able beneficiaries and eligibility for PES referral.

### Business Rules

#### BR.EMPL.01.01  Work Capacity Assessment
- **Rule**: Beneficiaries MUST have a documented work capacity assessment before referral
- **Validation**: Assessment date within last 12 months AND assessment result indicates "work-able"
- **Exception**: Emergency referrals may proceed with provisional assessment, requiring confirmation within 30 days

#### BR.EMPL.01.02  Age Eligibility
- **Rule**: Beneficiaries MUST be within working age range (18-65 years, country-specific)
- **Validation**: Age calculated from date of birth falls within configured min/max range
- **Configuration**: Min/max ages configurable per country legal framework

#### BR.EMPL.01.03  Benefit Status Requirement
- **Rule**: Beneficiaries MUST have active SP benefits to be eligible for referral
- **Validation**: Active benefit record exists AND payment status is "current" or "suspended_temporary"
- **Exception**: Recently terminated benefits (within 30 days) may qualify for re-activation referral

#### BR.EMPL.01.04  Legal Basis Verification
- **Rule**: Data sharing MUST have valid legal basis (consent OR inter-agency agreement)
- **Validation**: Consent record exists AND is current OR data sharing agreement covers employment services
- **Audit**: All data sharing decisions MUST be logged with legal basis justification

### Validation Logic

```json
{
  "referral_eligibility": {
    "required_conditions": [
      {
        "condition": "work_capacity_assessment",
        "validation": "assessment_date >= (current_date - 365 days) AND work_able = true"
      },
      {
        "condition": "age_eligibility",
        "validation": "age >= country_config.min_working_age AND age <= country_config.max_working_age"
      },
      {
        "condition": "benefit_status",
        "validation": "benefit_status IN ['active', 'suspended_temporary'] OR (benefit_end_date >= current_date - 30 days)"
      },
      {
        "condition": "legal_basis",
        "validation": "consent_valid = true OR data_sharing_agreement = true"
      }
    ],
    "outcome": "eligible_for_referral"
  }
}
```

### Assumptions
- Work capacity assessments use standardized criteria across SP programs
- PES has capacity to receive referrals (no quota system assumed)
- Beneficiary can be contacted within reasonable timeframe (valid contact information)
- `TODO(evidence): Country-specific working age definitions and capacity assessment standards`

---

## ASM.EMPL.02  Employment Status Data Quality

**Scope**: Defines data quality standards and confidence scoring for employment verification.

### Business Rules

#### BR.EMPL.02.01  Identity Matching
- **Rule**: Person identity MUST be resolved with high confidence before status verification
- **Validation**: Minimum 2 matching identifiers (national ID + 1 other) OR biometric match
- **Threshold**: Identity confidence score e 0.85 required for verification

#### BR.EMPL.02.02  Source Reliability
- **Rule**: Employment status MUST be verified from multiple authoritative sources
- **Validation**: At least 2 of: Tax Authority, PES records, Employer direct, Social Security
- **Confidence**: Higher source count increases overall confidence score

#### BR.EMPL.02.03  Data Freshness
- **Rule**: Employment data MUST be current within acceptable staleness window
- **Validation**: Latest data point within 30 days for real-time, 90 days for batch verification
- **Degradation**: Confidence score decreases with data age

#### BR.EMPL.02.04  Conflicting Information Resolution
- **Rule**: Conflicting employment status data MUST be flagged for manual review
- **Validation**: Sources agree on employment status OR discrepancy explicitly documented
- **Escalation**: High-value cases (income thresholds) require priority resolution

### Validation Logic

```json
{
  "employment_verification": {
    "identity_matching": {
      "minimum_identifiers": 2,
      "confidence_threshold": 0.85,
      "acceptable_types": ["national_id", "tax_id", "social_security_number", "biometric"]
    },
    "source_requirements": {
      "minimum_sources": 2,
      "authoritative_sources": ["tax_authority", "pes_records", "employer_direct", "social_security"],
      "confidence_weights": {
        "tax_authority": 0.4,
        "employer_direct": 0.3,
        "pes_records": 0.2,
        "social_security": 0.1
      }
    },
    "data_freshness": {
      "real_time_max_age_days": 30,
      "batch_max_age_days": 90,
      "confidence_degradation_factor": 0.02
    },
    "confidence_calculation": {
      "algorithm": "weighted_average_with_freshness_decay",
      "minimum_threshold": 0.75,
      "formula": {
        "base_score": "sum(source_weight * source_reliability)",
        "age_weeks": "(current_date - latest_data_date) / 7",
        "freshness_factor": "max(0.5, 1 - (age_weeks * 0.02))",
        "final_score": "base_score * freshness_factor"
      },
      "bounds": {"min": 0.0, "max": 1.0}
    }
  }
}
```

### Assumptions
- Tax authorities provide timely employment data feeds
- Employers report employment status changes promptly
- PES maintains accurate records of job placements and terminations
- `TODO(evidence): Country-specific data sharing agreements and update frequencies`

---

## ASM.EMPL.03  Training Program and Benefit Coordination

**Scope**: Defines rules for coordinating training enrollment with benefit continuation.

### Business Rules

#### BR.EMPL.03.01  Training Eligibility
- **Rule**: Beneficiaries MUST meet training program prerequisites and availability
- **Validation**: Skills assessment indicates training need AND schedule compatible with obligations
- **Prerequisites**: Basic literacy/numeracy levels, health requirements, geographic accessibility

#### BR.EMPL.03.02  Benefit Continuation Rules
- **Rule**: Training participation MUST maintain benefit eligibility with possible enhancement
- **Validation**: Training hours e job search requirement hours OR training allowance supplements base benefit
- **Enhancement**: Training allowance may increase total benefit amount temporarily

#### BR.EMPL.03.03  Program Capacity Management
- **Rule**: Training enrollment MUST verify program capacity before commitment
- **Validation**: Available slots > 0 AND waitlist position acceptable (if applicable)
- **Reservation**: Slot reservation valid for 5 working days pending SP approval

#### BR.EMPL.03.04  Progress Monitoring Requirements
- **Rule**: Training progress MUST be monitored and reported to maintain benefits
- **Validation**: Attendance e 80% AND satisfactory progress as defined by provider
- **Intervention**: Poor attendance triggers counseling before benefit suspension

### Validation Logic

```json
{
  "training_enrollment": {
    "eligibility_checks": {
      "skills_assessment": "completed AND training_recommended = true",
      "schedule_compatibility": "available_hours >= training_hours",
      "prerequisites": "meets_basic_requirements = true"
    },
    "benefit_calculation": {
      "continuation_rule": "training_hours >= job_search_hours OR training_supplement > 0",
      "maximum_duration": "country_config.max_training_months",
      "allowance_calculation": "base_benefit + training_supplement"
    },
    "progress_monitoring": {
      "minimum_attendance": 0.80,
      "reporting_frequency": "weekly",
      "intervention_threshold": 0.70
    }
  }
}
```

### Assumptions
- Training providers use standardized progress reporting
- Benefit systems can accommodate variable payment schedules
- Geographic accessibility is factored into program matching
- `TODO(evidence): Country-specific training allowance structures and monitoring requirements`

---

## ASM.EMPL.04  Job Placement and Employment Sustainability

**Scope**: Defines criteria for successful job placement and employment stability monitoring.

### Business Rules

#### BR.EMPL.04.01  Suitable Employment Definition
- **Rule**: Job placements MUST meet minimum suitability criteria for benefit impact
- **Validation**: Hours e minimum threshold AND wage e minimum threshold AND contract type acceptable
- **Thresholds**: Minimum 20 hours/week, wage e 60% of previous income or minimum wage, fixed-term e 6 months

#### BR.EMPL.04.02  Employment Verification
- **Rule**: Job placements MUST be verified by independent sources before benefit adjustment
- **Validation**: Employer confirmation AND (tax record OR social security record) within 30 days
- **Documentation**: Employment contract or offer letter required for placement registration

#### BR.EMPL.04.03  Stability Monitoring Period
- **Rule**: Employment MUST be monitored for minimum stability period before graduation
- **Validation**: Continuous employment for 90 days with allowance for brief gaps (d5 days)
- **Verification**: Monthly employment status checks via automated systems

#### BR.EMPL.04.04  Support Services Coordination
- **Rule**: Placement support services MUST be available during transition period
- **Validation**: Transportation, childcare, or other identified barriers addressed
- **Duration**: Support services available for 6 months post-placement

### Validation Logic

```json
{
  "job_placement": {
    "suitability_criteria": {
      "minimum_hours_per_week": 20,
      "minimum_wage_factor": 0.60,
      "minimum_wage_absolute": "country_config.minimum_wage",
      "acceptable_contract_types": ["permanent", "fixed_term_6months+", "probationary"]
    },
    "verification_requirements": {
      "employer_confirmation": "required",
      "independent_verification": "tax_record OR social_security_record",
      "verification_timeline_days": 30,
      "required_documentation": ["employment_contract", "job_offer"]
    },
    "stability_monitoring": {
      "minimum_period_days": 90,
      "maximum_gap_days": 5,
      "verification_frequency": "monthly",
      "graduation_trigger": "stable_90_days = true"
    }
  }
}
```

### Assumptions
- Employers cooperate with employment verification processes
- Automated employment verification systems are reliable
- Support services are available and accessible in relevant geographic areas
- `TODO(evidence): Country-specific employment quality standards and support service availability`

---

## ASM.EMPL.05  Benefit Graduation and Transition Management

**Scope**: Defines rules for systematic benefit reduction and graduation upon employment success.

### Business Rules

#### BR.EMPL.05.01  Graduation Trigger Criteria
- **Rule**: Graduation MUST be triggered only after confirmed employment stability
- **Validation**: 90-day employment stability AND income stability e benefit threshold
- **Income Threshold**: Monthly employment income e 120% of monthly benefit amount

#### BR.EMPL.05.02  Graduated Reduction Schedule
- **Rule**: Benefits MUST be reduced gradually over defined timeline
- **Validation**: 4-month reduction schedule: 75%, 50%, 25%, 0% of original benefit
- **Flexibility**: Schedule may be extended for vulnerable populations or unstable employment

#### BR.EMPL.05.03  Re-entry Protection
- **Rule**: Graduated beneficiaries MUST have fast-track re-entry if employment lost
- **Validation**: Employment loss within 12 months of graduation qualifies for expedited processing
- **Timeline**: Fast-track applications processed within 15 working days vs. standard 45 days

#### BR.EMPL.05.04  Emergency Support Availability
- **Rule**: Emergency assistance MUST be available during grace period
- **Validation**: One-time emergency payment available if employment lost within 6 months
- **Limitation**: Emergency support limited to 50% of previous benefit for up to 3 months

### Validation Logic

```json
{
  "benefit_graduation": {
    "trigger_criteria": {
      "employment_stability_days": 90,
      "income_threshold_factor": 1.20,
      "verification_required": ["employment_verification", "income_verification"]
    },
    "reduction_schedule": {
      "phase_duration_months": 1,
      "reduction_percentages": [75, 50, 25, 0],
      "vulnerable_population_extension": "possible",
      "minimum_notice_days": 30
    },
    "reentry_protection": {
      "grace_period_months": 12,
      "fast_track_processing_days": 15,
      "standard_processing_days": 45
    },
    "emergency_support": {
      "availability_period_months": 6,
      "maximum_amount_factor": 0.50,
      "maximum_duration_months": 3
    },
    "grace_period_definitions": {
      "reentry_grace_period_months": 12,
      "emergency_support_period_months": 6,
      "description": "Both periods start from graduation date and run concurrently",
      "purpose": {
        "reentry_grace": "Fast-track re-application processing (15 vs 45 days)",
        "emergency_support": "One-time emergency payment if employment lost"
      }
    }
  }
}
```

### Assumptions
- Income verification systems provide accurate and timely data
- Beneficiaries understand and accept graduation timeline
- Emergency support systems can be activated quickly
- `TODO(evidence): Country-specific graduation policies and emergency support mechanisms`

---

## Cross-Cutting Business Rules

### Data Governance and Compliance

#### BR.CROSS.01  Data Minimization
- **Rule**: Only minimum necessary data MUST be shared for each process
- **Validation**: Data purpose documented AND retention period defined AND access controls enforced
- **Audit**: Regular review of data sharing practices and consent validity

#### BR.CROSS.02  Audit Trail Requirements
- **Rule**: All system interactions MUST generate complete audit trail
- **Validation**: User identity + action + timestamp + data changed + business justification
- **Retention**: Audit logs retained for minimum 7 years or per legal requirements

#### BR.CROSS.03  Error Handling Standards
- **Rule**: System errors MUST NOT expose sensitive data or create benefit disruption
- **Validation**: Error responses use standardized codes AND fallback procedures exist
- **Recovery**: Failed transactions require manual review within 24 hours

### Performance and Availability

#### BR.CROSS.04  Response Time Requirements
- **Rule**: Critical processes MUST meet defined SLA response times
- **Validation**: Referral processing d5 seconds, Status verification d2 seconds
- **Monitoring**: SLA breaches trigger automatic alerts and escalation

#### BR.CROSS.05  System Availability
- **Rule**: Systems MUST maintain minimum uptime during business hours
- **Validation**: 99.5% availability during 8 AM - 6 PM, 95% outside business hours
- **Fallback**: Manual processes available when automated systems unavailable

### Security and Privacy

#### BR.CROSS.06  Authentication and Authorization
- **Rule**: All system access MUST use strong authentication with appropriate authorization
- **Validation**: OAuth2 with JWT tokens AND role-based access control
- **Session**: Maximum session duration 8 hours with re-authentication required

#### BR.CROSS.07  Data Encryption
- **Rule**: All sensitive data MUST be encrypted in transit and at rest
- **Validation**: TLS 1.3+ for transit, AES-256 for storage
- **Key Management**: Encryption keys rotated every 12 months or per breach

---

## Implementation Validation Framework

### Rule Engine Configuration

```json
{
  "rule_engine": {
    "evaluation_mode": "strict",
    "error_handling": "fail_safe",
    "rule_versioning": "semantic",
    "audit_level": "comprehensive"
  },
  "validation_levels": {
    "level_1": "syntax_validation",
    "level_2": "business_rule_validation",
    "level_3": "cross_system_consistency",
    "level_4": "compliance_verification"
  },
  "exception_handling": {
    "escalation_timeout": "24_hours",
    "manual_review_required": true,
    "business_continuity": "maintain_current_status"
  }
}
```

### Testing Requirements

#### Unit Testing
- All business rules MUST have automated unit tests
- Test coverage e95% for rule validation logic
- Edge cases and boundary conditions included

#### Integration Testing
- Cross-system rule validation scenarios
- End-to-end workflow compliance testing
- Performance testing under load

#### User Acceptance Testing
- Business stakeholder validation of rules
- Country-specific configuration testing
- Compliance officer approval required

---

## Configuration Management

### Country-Specific Parameters

```json
{
  "country_config": {
    "working_age": {
      "minimum": 18,
      "maximum": 65
    },
    "benefit_thresholds": {
      "minimum_wage": 450.00,
      "graduation_income_factor": 1.20
    },
    "timeframes": {
      "verification_max_age_days": 30,
      "stability_period_days": 90,
      "graduation_notice_days": 30
    },
    "support_services": {
      "transportation_available": true,
      "childcare_available": true,
      "language_support": ["spanish", "english"]
    }
  }
}
```

### Rule Versioning and Updates

- **Semantic Versioning**: Rules follow semantic versioning (major.minor.patch)
- **Backward Compatibility**: New versions maintain compatibility for 12 months
- **Migration Strategy**: Phased rollout with validation periods
- **Rollback Capability**: Ability to revert to previous rule version within 48 hours

---

## Quality Assurance and Monitoring

### Continuous Monitoring

#### Business Rule Performance
- **Rule Execution Time**: Track average execution time per rule
- **Rule Success Rate**: Monitor rule validation pass/fail rates
- **Exception Frequency**: Alert on unusual exception patterns

#### Data Quality Metrics
- **Completeness**: Percentage of required fields populated
- **Accuracy**: Verification success rates across data sources
- **Timeliness**: Average age of data used in decisions

#### Compliance Reporting
- **Audit Trail Completeness**: 100% of transactions logged
- **Data Retention Compliance**: Automated purging per retention policies
- **Access Control Violations**: Real-time monitoring and alerting

---

`TODO(legal): Validate business rules against country-specific legal frameworks`
`TODO(technical): Implement rule engine configuration and testing framework`
`TODO(governance): Establish rule change management and approval processes`