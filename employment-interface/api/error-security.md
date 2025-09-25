# Employment-SP API Error Handling and Security

This document defines comprehensive error handling patterns and security frameworks for Employment-Social Protection API integrations, following DCI standards with employment domain extensions.

## DCI-Compliant Error Handling

### DCI Three-Part Response Structure with Employment Extensions

All Employment-SP API responses follow the DCI Response.yaml pattern with employment-specific error extensions:

```json
{
  "signature": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "header": {
    "message_id": "550e8400-e29b-41d4-a716-446655440002",
    "message_ts": "2025-09-15T14:30:05.000Z",
    "action": "referral",
    "status": "rjct",
    "status_reason_code": "EMPL_VALIDATION_ERROR",
    "status_reason_message": "Employment referral validation failed",
    "sender_id": "PES-CL",
    "receiver_id": "SP-MIS-CL",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440000"
  },
  "message": {
    "ack_status": "ERR",
    "timestamp": "2025-09-15T14:30:05.000Z",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440000",
    "error": {
      "code": "err.employment.validation_failed",
      "message": "Employment referral validation failed - work capacity assessment required"
    }
  }
}
```

### DCI Standard Error Codes (Employment Extensions)

**Base DCI Error Codes** (from Error.yaml):
- `err.request.bad`: Malformed request
- `err.request.unauthorized`: Authentication failed
- `err.request.forbidden`: Authorization failed
- `err.signature.invalid`: Message signature verification failed
- `err.encryption.invalid`: Message encryption/decryption failed
- `err.service.unavailable`: Service temporarily unavailable

**Employment-Specific Extensions**:
- `err.employment.validation_failed`: Employment data validation failures
- `err.employment.business_rule_violation`: Employment business logic violations
- `err.employment.work_capacity_required`: Work capacity assessment required
- `err.employment.jurisdiction_mismatch`: Geographic jurisdiction mismatch
- `err.employment.consent_required`: Employment service consent required
- `err.employment.compliance_violation`: Employment compliance violation
- `err.employment.programme_inactive`: Source programme is inactive
- `err.employment.duplicate_referral`: Duplicate referral detected

### DCI Acknowledgment Status

Following DCI Ack.yaml pattern:
- `ACK`: Request valid, async callback will be invoked
- `NACK`: Request valid, no further updates from receiver
- `ERR`: Request invalid, cannot be processed

### Employment HTTP Error Response Structure

Following DCI HttpErrorResponse.yaml with employment extensions:

```json
{
  "errors": [
    {
      "code": "err.employment.validation_failed",
      "message": "Work capacity assessment required before referral creation"
    }
  }
}
```

---

## Employment-Specific Error Codes

### EMPL_VALIDATION_ERROR (400 Bad Request)

**Referral Validation Errors**:
- `WORK_CAPACITY_REQUIRED`: Person must be assessed as work-able
- `INVALID_TARGET_PES_OFFICE`: Target PES office does not serve person's area
- `MISSING_CONSENT`: Employment service consent required
- `DUPLICATE_ACTIVE_REFERRAL`: Person already has active referral
- `INVALID_SOURCE_PROGRAMME`: Source programme is inactive or invalid

**Employment Status Validation Errors**:
- `PERSON_NOT_FOUND`: Person identifier not found in employment systems
- `INSUFFICIENT_VERIFICATION_SOURCES`: Minimum verification sources not met
- `INCOME_DATA_STALE`: Income verification older than 30 days
- `CONFLICTING_EMPLOYMENT_STATUS`: Inconsistent status across verification sources

**Training Benefit Validation Errors**:
- `TRAINING_PREREQUISITES_NOT_MET`: Person does not meet training requirements
- `TRAINING_PROVIDER_NOT_ACCREDITED`: Training provider lacks required accreditation
- `BUDGET_ALLOCATION_UNAVAILABLE`: Insufficient budget for training enrollment
- `CONFLICTING_TRAINING_ENROLLMENT`: Person already enrolled in conflicting training

**Country-Specific Validation Errors**:
- `CHILE_RSH_CLASSIFICATION_INVALID`: Chilean social registry classification incomplete or invalid
- `CHILE_CLAVE_UNICA_VERIFICATION_FAILED`: ClaveÚnica digital identity verification failed
- `KOREA_WORK24_SYNC_ERROR`: Work24 platform synchronization failure
- `KOREA_NBLSS_INTEGRATION_UNAVAILABLE`: NBLSS welfare system temporarily unavailable
- `GERMANY_SGB_COMPLIANCE_VIOLATION`: Social Code Books II/III compliance violation detected
- `GERMANY_BILDUNGSGUTSCHEIN_LIMIT_EXCEEDED`: Training voucher allocation limit exceeded
- `AUSTRALIA_JOBACTIVE_PROVIDER_UNAVAILABLE`: JobActive provider network connectivity issue
- `AUSTRALIA_MUTUAL_OBLIGATION_CONFLICT`: Conflicting mutual obligation requirements
- `TURKEY_ISAF_BENEFICIARY_STATUS_UNKNOWN`: ISAF social assistance status cannot be verified

### EMPL_BUSINESS_RULE_VIOLATION (422 Unprocessable Entity)

**Work Capacity Business Rules**:
```json
{
  "error": {
    "code": "EMPL_BUSINESS_RULE_VIOLATION",
    "message": "Work capacity assessment indicates person is not available for full-time employment",
    "category": "business_rule_violation",
    "details": [
      {
        "rule": "FULL_TIME_AVAILABILITY_REQUIRED",
        "condition": "employment_type = 'permanent_full_time' AND work_capacity.availability != 'full_time'",
        "current_values": {
          "employment_type": "permanent_full_time",
          "work_capacity.availability": "part_time_only"
        },
        "suggested_resolution": "Update employment type to 'permanent_part_time' or reassess work capacity"
      }
    ]
  }
}
```

**Geographic Jurisdiction Rules**:
```json
{
  "error": {
    "code": "EMPL_BUSINESS_RULE_VIOLATION",
    "message": "Person's residence is outside target PES office jurisdiction",
    "category": "business_rule_violation",
    "details": [
      {
        "rule": "GEOGRAPHIC_JURISDICTION_REQUIRED",
        "condition": "person.address.region IN target_pes_office.service_areas",
        "current_values": {
          "person.address.region": "RM_Santiago",
          "target_pes_office.service_areas": ["Region_Valparaiso", "Region_Aconcagua"]
        },
        "suggested_resolution": "Route referral to appropriate regional PES office"
      }
    ]
  }
}
```

**Country-Specific Business Rules**:
```json
{
  "error": {
    "code": "EMPL_BUSINESS_RULE_VIOLATION",
    "message": "Chile RSH vulnerability percentile requirement not met for employment subsidy",
    "category": "business_rule_violation",
    "details": [
      {
        "rule": "CHILE_RSH_VULNERABILITY_THRESHOLD",
        "condition": "rsh_percentile <= 60 AND employment_subsidy_requested = true",
        "current_values": {
          "rsh_percentile": 75,
          "employment_subsidy_requested": true
        },
        "country_specific": "CL-RSH-SENCE",
        "suggested_resolution": "Person not eligible for employment subsidy based on RSH classification"
      }
    ]
  }
}
```

```json
{
  "error": {
    "code": "EMPL_BUSINESS_RULE_VIOLATION",
    "message": "South Korea Work24 AI matching confidence below threshold",
    "category": "business_rule_violation",
    "details": [
      {
        "rule": "KOREA_AI_MATCHING_CONFIDENCE_THRESHOLD",
        "condition": "ai_confidence_score >= 0.75 FOR job_matching_recommendations",
        "current_values": {
          "ai_confidence_score": 0.62,
          "minimum_required": 0.75
        },
        "country_specific": "KR-WORK24",
        "suggested_resolution": "Additional skills assessment required for reliable AI matching"
      }
    ]
  }
}
```

```json
{
  "error": {
    "code": "EMPL_BUSINESS_RULE_VIOLATION",
    "message": "Germany Bildungsgutschein training duration exceeds maximum allowed",
    "category": "business_rule_violation",
    "details": [
      {
        "rule": "GERMANY_BILDUNGSGUTSCHEIN_DURATION_LIMIT",
        "condition": "training_duration_months <= 24 AND voucher_value <= 15000_EUR",
        "current_values": {
          "training_duration_months": 30,
          "voucher_value": 18000,
          "currency": "EUR"
        },
        "country_specific": "DE-SGB",
        "suggested_resolution": "Split training into multiple vouchers or reduce duration"
      }
    ]
  }
```

### EMPL_AUTHORIZATION_ERROR (403 Forbidden)

**Scope-Based Authorization**:
```json
{
  "error": {
    "code": "EMPL_AUTHORIZATION_ERROR",
    "message": "Insufficient scope for employment status verification",
    "category": "authorization_error",
    "details": [
      {
        "required_scope": "employment:read",
        "current_scopes": ["referral:create", "benefit:read"],
        "operation": "GET /employment-status/{person_id}",
        "suggested_action": "Request employment:read scope from authorization server"
      }
    ]
  }
}
```

**System-Level Authorization**:
```json
{
  "error": {
    "code": "EMPL_AUTHORIZATION_ERROR",
    "message": "SP system not authorized to access PES employment placement data",
    "category": "authorization_error",
    "details": [
      {
        "requesting_system": "sp-mis",
        "target_resource": "job_placement_details",
        "authorization_level": "basic_sp_integration",
        "required_level": "advanced_sp_integration",
        "suggested_action": "Upgrade integration agreement to access placement details"
      }
    ]
  }
}
```

### EMPL_SYSTEM_INTEGRATION_ERROR (502 Bad Gateway / 503 Service Unavailable)

**PES System Unavailable**:
```json
{
  "error": {
    "code": "EMPL_SYSTEM_INTEGRATION_ERROR",
    "message": "Target PES system temporarily unavailable",
    "category": "system_integration_error",
    "details": [
      {
        "target_system": "pes-casework-module",
        "last_successful_connection": "2025-09-15T13:45:00.000Z",
        "error_duration": "15_minutes",
        "estimated_recovery": "2025-09-15T15:00:00.000Z"
      }
    ],
    "retry_guidance": {
      "retryable": true,
      "retry_after_seconds": 900,
      "max_retries": 5,
      "alternative_endpoints": ["pes-backup-service"]
    }
  }
}
```

**Cross-System Data Synchronization Error**:
```json
{
  "error": {
    "code": "EMPL_DATA_CONSISTENCY_ERROR",
    "message": "Employment status inconsistent between PES and tax authority systems",
    "category": "data_consistency_error",
    "details": [
      {
        "person_id": "person:12345",
        "pes_employment_status": "employed",
        "tax_authority_status": "unemployed",
        "last_sync_timestamp": "2025-09-14T22:00:00.000Z",
        "resolution_strategy": "manual_verification_required"
      }
    ],
    "suggested_actions": [
      "Escalate to data quality team",
      "Request manual verification from caseworker",
      "Use most recent verified source pending resolution"
    ]
  }
}
```

---

## Security Framework

### Authentication and Authorization

**OAuth2 Implementation for Employment Services**:

```json
{
  "oauth2_configuration": {
    "authorization_server": "https://auth.employment-sp.dci.gov",
    "token_endpoint": "https://auth.employment-sp.dci.gov/oauth2/token",
    "jwks_uri": "https://auth.employment-sp.dci.gov/.well-known/jwks.json",
    "supported_flows": ["client_credentials"],
    "supported_scopes": [
      "referral:create",
      "referral:read",
      "referral:update",
      "employment:read",
      "employment:verify",
      "benefit:manage",
      "benefit:read",
      "placement:create",
      "placement:read",
      "placement:update",
      "compliance:read",
      "compliance:monitor",
      "notification:send",
      "webhook:manage"
    ]
  }
}
```

**JWT Token Structure**:
```json
{
  "header": {
    "alg": "RS256",
    "typ": "JWT",
    "kid": "employment-sp-key-2024"
  },
  "payload": {
    "iss": "https://auth.employment-sp.dci.gov",
    "sub": "sp-mis-client-001",
    "aud": "https://api.employment-sp.dci.gov",
    "exp": 1695651000,
    "iat": 1695647400,
    "scope": "referral:create employment:read benefit:manage",
    "client_type": "sp_system",
    "system_role": "social_protection_authority",
    "jurisdiction": "CL-RM",
    "purpose_of_use": "employment_service_integration",
    "data_classification": "employment_sensitive"
  }
}
```

### Employment Data Protection

**Field-Level Encryption for Sensitive Data**:

```json
{
  "encryption_configuration": {
    "algorithm": "AES-256-GCM",
    "key_management": "HSM_managed",
    "encrypted_fields": [
      "person.identifiers.national_id",
      "employment_details.exact_salary",
      "medical_restrictions.specific_conditions",
      "contact_info.detailed_address"
    ],
    "encryption_context": {
      "purpose": "employment_service_integration",
      "jurisdiction": "chile",
      "data_classification": "employment_pii"
    }
  }
}
```

**Example Encrypted Employment Message**:
```json
{
  "person": {
    "identifiers": [
      {
        "identifier_type": "national_id",
        "identifier_value": "encrypted:AES256:eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..rQjz8vvV5fU5qzw2.encrypted_value.tag",
        "encryption_key_id": "employment-pii-key-2024-q3"
      }
    ],
    "employment_details": {
      "salary_range": "450000-500000_CLP",
      "exact_salary": "encrypted:AES256:detailed_salary_data_encrypted"
    }
  }
}
```

### Data Minimization and Privacy Controls

**Purpose-Based Data Filtering**:

```json
{
  "data_minimization_rules": {
    "referral_processing": {
      "required_fields": [
        "person.identifiers",
        "person.name",
        "work_capacity_assessment",
        "source_programme"
      ],
      "optional_fields": [
        "person.contact_info.phone",
        "person.demographics.age_range"
      ],
      "excluded_fields": [
        "person.medical_history",
        "person.financial_details",
        "employment_history.salary_history"
      ]
    },
    "compliance_monitoring": {
      "required_fields": [
        "person.identifiers",
        "compliance_obligations",
        "employment_status.current_status"
      ],
      "excluded_fields": [
        "employment_details.salary",
        "personal_circumstances.detailed_family_info"
      ]
    }
  }
}
```

**Dynamic Privacy Controls**:
```json
{
  "privacy_controls": {
    "requesting_system_role": "sp_compliance_officer",
    "purpose_of_use": "benefit_eligibility_verification",
    "legal_basis": "social_protection_law_article_15",
    "consent_status": "explicit_consent_given",
    "data_retention_period": "24_months",
    "applied_filters": [
      "exclude_medical_details",
      "aggregate_income_bands",
      "anonymize_employer_details"
    ]
  }
}
```

### Audit and Compliance Logging

**Comprehensive Audit Trail**:

```json
{
  "audit_event": {
    "event_id": "550e8400-e29b-41d4-a716-446655440010",
    "timestamp": "2025-09-15T14:30:00.000Z",
    "event_type": "employment_data_access",
    "actor": {
      "system": "sp-mis",
      "user": "caseworker_123",
      "role": "sp_compliance_officer",
      "authentication_method": "oauth2_jwt"
    },
    "target": {
      "resource_type": "employment_status",
      "resource_id": "person:12345",
      "person_identifier": "hashed_person_id_for_audit"
    },
    "operation": {
      "method": "GET",
      "endpoint": "/employment-status/person:12345",
      "purpose_of_use": "benefit_eligibility_verification",
      "legal_basis": "Law_20379_article_12"
    },
    "data_accessed": {
      "fields": [
        "current_employment_status",
        "employment_start_date",
        "income_range"
      ],
      "sensitive_data_accessed": false,
      "pii_accessed": "minimal"
    },
    "result": {
      "status": "success",
      "response_code": 200,
      "data_returned": true,
      "privacy_filters_applied": ["income_range_aggregation"]
    },
    "compliance": {
      "gdpr_article6_basis": "public_interest",
      "data_protection_impact": "low",
      "retention_period": "24_months",
      "deletion_scheduled": "2027-09-15"
    }
  }
}
```

**Compliance Monitoring Dashboard Data**:
```json
{
  "compliance_metrics": {
    "time_period": "2025-09-01_to_2025-09-15",
    "total_api_calls": 15427,
    "by_operation_type": {
      "referral_creation": 3245,
      "employment_verification": 8932,
      "benefit_enrollment": 2104,
      "compliance_monitoring": 1146
    },
    "privacy_compliance": {
      "purpose_of_use_documented": "100%",
      "consent_verified": "98.7%",
      "data_minimization_applied": "100%",
      "retention_policies_enforced": "100%"
    },
    "security_metrics": {
      "authentication_failures": 23,
      "authorization_denials": 156,
      "encryption_compliance": "100%",
      "audit_trail_completeness": "100%"
    }
  }
}
```

---

## Security Threat Prevention

### Input Validation and Sanitization

**Employment Data Validation Rules**:

```json
{
  "validation_rules": {
    "person_identifiers": {
      "pattern": "^[A-Z0-9]{8,20}$",
      "length": {"min": 8, "max": 20},
      "sanitization": "alphanumeric_only",
      "injection_prevention": true
    },
    "monetary_amounts": {
      "pattern": "^\\d{1,10}(\\.\\d{1,2})?$",
      "range": {"min": 0, "max": 99999999.99},
      "sanitization": "numeric_decimal_only"
    },
    "employment_dates": {
      "format": "ISO_8601_date",
      "range": {"min": "1950-01-01", "max": "2050-12-31"},
      "validation": "future_date_check"
    }
  }
}
```

**SQL Injection Prevention**:
```json
{
  "query_protection": {
    "parameterized_queries": "required",
    "input_escaping": "automatic",
    "query_whitelist": "employment_approved_queries",
    "dangerous_patterns": [
      "'; DROP TABLE",
      "UNION SELECT",
      "OR 1=1",
      "<script>",
      "javascript:"
    ]
  }
}
```

### Rate Limiting and DDoS Protection

**Employment-Specific Rate Limits**:

```json
{
  "rate_limiting": {
    "by_operation_type": {
      "referral_creation": {
        "requests_per_hour": 1000,
        "burst_limit": 50,
        "penalty_period_minutes": 15
      },
      "employment_verification": {
        "requests_per_hour": 5000,
        "burst_limit": 100,
        "penalty_period_minutes": 5
      },
      "bulk_operations": {
        "requests_per_hour": 100,
        "max_items_per_request": 100,
        "penalty_period_minutes": 60
      }
    },
    "by_client_type": {
      "sp_system": {
        "base_limit_multiplier": 1.0,
        "priority": "high"
      },
      "pes_system": {
        "base_limit_multiplier": 1.0,
        "priority": "high"
      },
      "training_provider": {
        "base_limit_multiplier": 0.5,
        "priority": "medium"
      }
    }
  }
}
```

**DDoS Protection Patterns**:
```json
{
  "ddos_protection": {
    "detection_thresholds": {
      "requests_per_second": 1000,
      "concurrent_connections": 5000,
      "error_rate_threshold": 0.1
    },
    "mitigation_strategies": [
      "rate_limiting_escalation",
      "geographic_filtering",
      "client_reputation_scoring",
      "challenge_response_verification"
    ],
    "incident_response": {
      "auto_scaling": "enabled",
      "traffic_shaping": "enabled",
      "emergency_contact": "security@employment-sp.dci.gov"
    }
  }
}
```

### API Security Best Practices

**Secure Headers**:
```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
Content-Security-Policy: default-src 'self'; script-src 'none'; object-src 'none'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

**CORS Configuration for Employment Services**:
```json
{
  "cors_policy": {
    "allowed_origins": [
      "https://sp-portal.gobierno.cl",
      "https://pes-casework.trabajo.cl",
      "https://training-portal.sence.cl"
    ],
    "allowed_methods": ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    "allowed_headers": [
      "Authorization",
      "Content-Type",
      "X-Correlation-ID",
      "X-Request-ID",
      "X-API-Version"
    ],
    "max_age": 3600,
    "credentials": false
  }
}
```

---

## Incident Response and Recovery

### Security Incident Classification

**Employment Data Breach Response**:

```json
{
  "incident_classification": {
    "severity_levels": {
      "critical": {
        "description": "Large-scale employment PII exposure",
        "response_time": "15_minutes",
        "notification_required": ["DPO", "CISO", "legal_team", "affected_authorities"]
      },
      "high": {
        "description": "Unauthorized access to employment records",
        "response_time": "1_hour",
        "notification_required": ["security_team", "system_administrators"]
      },
      "medium": {
        "description": "API abuse or excessive data requests",
        "response_time": "4_hours",
        "notification_required": ["api_operations_team"]
      }
    }
  }
}
```

**Automated Response Actions**:
```json
{
  "automated_responses": {
    "suspicious_activity_detected": [
      "temporary_client_suspension",
      "additional_authentication_required",
      "security_team_notification"
    ],
    "data_breach_suspected": [
      "immediate_api_isolation",
      "forensic_logging_activation",
      "emergency_contact_notification"
    ],
    "system_compromise_detected": [
      "full_service_shutdown",
      "backup_system_activation",
      "law_enforcement_notification"
    ]
  }
}
```

### Business Continuity

**Employment Service Continuity Planning**:

```json
{
  "continuity_plan": {
    "critical_functions": [
      "employment_referral_processing",
      "benefit_eligibility_verification",
      "compliance_monitoring",
      "emergency_job_placement_services"
    ],
    "backup_procedures": {
      "manual_referral_processing": {
        "activation_threshold": "api_unavailable_for_30_minutes",
        "process": "paper_based_referral_with_24_hour_digital_sync"
      },
      "offline_employment_verification": {
        "activation_threshold": "employment_db_unavailable",
        "process": "phone_verification_with_audit_trail"
      }
    },
    "recovery_priorities": [
      "employment_referral_api",
      "employment_status_verification",
      "benefit_enrollment_services",
      "compliance_monitoring_dashboard"
    ]
  }
}
```

---

## Monitoring and Alerting

### Security Monitoring

**Real-time Security Alerts**:

```json
{
  "security_alerts": {
    "authentication_anomalies": {
      "threshold": "10_failed_attempts_per_minute",
      "action": "temporary_ip_block",
      "notification": "security_team_immediate"
    },
    "data_access_patterns": {
      "threshold": "unusual_bulk_data_requests",
      "action": "additional_verification_required",
      "notification": "data_protection_officer"
    },
    "api_abuse_detection": {
      "threshold": "rate_limit_exceeded_repeatedly",
      "action": "client_suspension",
      "notification": "api_security_team"
    }
  }
}
```

**Performance and Security Metrics**:
```json
{
  "monitoring_dashboard": {
    "security_kpis": {
      "authentication_success_rate": "> 99.5%",
      "authorization_accuracy": "> 99.9%",
      "data_breach_incidents": "0",
      "security_response_time": "< 15_minutes"
    },
    "api_performance": {
      "availability": "> 99.9%",
      "response_time_p95": "< 2_seconds",
      "error_rate": "< 0.1%",
      "throughput": "> 10000_requests_per_hour"
    },
    "compliance_tracking": {
      "audit_trail_completeness": "100%",
      "data_retention_compliance": "100%",
      "privacy_policy_adherence": "100%"
    }
  }
}
```

---

## Open Issues

`TODO(penetration_testing)`: Establish regular penetration testing schedule for employment-specific API endpoints and data flows.

`TODO(security_certification)`: Pursue relevant security certifications (ISO 27001, SOC 2) for employment data handling compliance.

`TODO(threat_modeling)`: Complete comprehensive threat modeling for employment data flows across PES-SP integrations.

`TODO(security_training)`: Develop security awareness training specific to employment data handling and API security best practices.