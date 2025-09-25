# Employment-SP API Methods

This document defines API endpoints for Employment-Social Protection interoperability, following DCI search/response patterns.

## API.EMPL.01  POST /referrals

**Purpose**: Create employment referrals from SP systems to PES, supporting batch operations.

**Headers (required)**:
- `Authorization`: Bearer JWT token with `referral:create` scope
- `Content-Type`: application/json
- `X-Correlation-ID`: UUID v4 for end-to-end tracing
- `X-Request-ID`: UUID v4 for individual request identification
- `X-API-Version`: 1.0.0
- `Accept-Language`: Language preference for response messages

**Request Structure** (following DCI SearchRequest pattern):
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "referral_requests": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "timestamp": "2025-09-15T14:30:00.000Z",
      "referral_data": {
        "person": {
          "@type": "Person",
          "identifiers": [
            {
              "identifier_type": "national_id",
              "identifier_value": "12345678901"
            }
          ],
          "name": {
            "given_name": "Maria",
            "surname": "Rodriguez"
          }
        },
        "source_programme": {
          "programme_identifier": "SP-CASH-2024",
          "programme_name": "Family Emergency Income"
        },
        "referral_reason": "unemployment_benefit_activation",
        "target_pes_office": "PES-SANTIAGO-CENTRO",
        "compliance_requirements": {
          "job_search_obligation": true,
          "reporting_frequency": "monthly"
        }
      },
      "consent": {
        "consent_given": true,
        "consent_date": "2025-09-14T10:00:00.000Z",
        "consent_type": "employment_services",
        "legal_basis": "social_protection_law_2023"
      },
      "locale": "es-CL"
    }
  ]
}
```

**Response Structure** (following DCI SearchResponse pattern):
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440002",
  "referral_responses": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "timestamp": "2025-09-15T14:30:05.000Z",
      "status": "succ",
      "status_reason_code": "REFERRAL_CREATED",
      "status_reason_message": "Referral successfully created and assigned to caseworker",
      "data": {
        "referral_id": "550e8400-e29b-41d4-a716-446655440003",
        "pes_case_number": "PES-2025-001234",
        "assigned_caseworker": "caseworker:789",
        "appointment_scheduled": "2025-09-20T10:00:00.000Z",
        "next_steps": [
          "attend_initial_appointment",
          "complete_skills_assessment",
          "begin_job_search_activities"
        ]
      },
      "locale": "es-CL"
    }
  ]
}
```

**Status Codes**:
- `201 Created`: Referral(s) successfully created
- `400 Bad Request`: Invalid request format or missing required fields
- `401 Unauthorized`: Invalid or missing authentication token
- `403 Forbidden`: Insufficient permissions for referral creation
- `409 Conflict`: Duplicate referral or conflicting state
- `422 Unprocessable Entity`: Valid format but business rule violations

**Business Rules**:
- Person must be assessed as work-able before referral acceptance
- Source programme must be active and verified
- Target PES office must serve person's geographic area
- Consent required for employment service participation
- Batch size limited to 100 referrals per request

---

## API.EMPL.02  GET /employment-status/{person_id}

**Purpose**: Retrieve employment status verification for SP benefit compliance.

**Headers (required)**:
- `Authorization`: Bearer JWT token with `employment:read` scope
- `X-Correlation-ID`: UUID v4 for request tracing
- `X-Request-ID`: UUID v4 for individual request
- `X-API-Version`: 1.0.0
- `Accept-Language`: Language preference

**Path Parameters**:
- `person_id`: Unique person identifier (UUID v4 or national ID)

**Query Parameters**:
- `verification_date`: ISO 8601 date for historical status (optional)
- `include_history`: Boolean to include employment history (default: false)
- `verification_sources`: Comma-separated list of sources to check

**Response Structure**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440001",
  "person_id": "person:12345",
  "employment_status": {
    "current_status": "employed",
    "status_date": "2025-08-01",
    "verification_date": "2025-09-15",
    "confidence_level": "high",
    "verification_sources": ["payroll_system", "tax_authority"],
    "employment_details": {
      "employer_name": "Tech Solutions SPA",
      "employment_type": "permanent_full_time",
      "monthly_income": {
        "amount": "450000",
        "currency_code": "CLP"
      },
      "start_date": "2025-08-01"
    }
  },
  "employment_history": [
    {
      "employment_period": {
        "start_date": "2024-01-15",
        "end_date": "2025-07-30"
      },
      "employer_name": "Previous Company Ltd",
      "employment_type": "temporary_full_time",
      "termination_reason": "contract_completion"
    }
  ],
  "sp_benefit_impact": {
    "benefit_continuation_eligible": false,
    "graduation_triggered": true,
    "income_supplement_needed": false,
    "next_review_date": "2025-12-15"
  }
}
```

**Status Codes**:
- `200 OK`: Employment status successfully retrieved
- `404 Not Found`: Person not found or no employment data available
- `401 Unauthorized`: Invalid authentication
- `403 Forbidden`: Insufficient permissions
- `429 Too Many Requests`: Rate limit exceeded

---

## API.EMPL.03  POST /employment-benefits

**Purpose**: Create or update employment-related benefits (training, work opportunities).

**Request Structure**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "benefit_requests": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "timestamp": "2025-09-15T14:30:00.000Z",
      "benefit_data": {
        "person_id": "person:12345",
        "benefit_type": "training",
        "employment_benefit_subtype": "vocational_skills_training",
        "benefit_description": "Digital Marketing Skills Training Program",
        "training_details": {
          "training_provider": "Professional Institute ABC",
          "course_name": "Digital Marketing Fundamentals",
          "duration_weeks": 12,
          "start_date": "2025-10-01",
          "end_date": "2025-12-20"
        },
        "benefit_value": {
          "amount": "150000",
          "currency_code": "CLP"
        },
        "stipend": {
          "amount": "50000",
          "currency_code": "CLP",
          "frequency": "monthly"
        },
        "attendance_requirement": 0.80
      },
      "authorization": {
        "approved_by": "pes-system",
        "approval_date": "2025-09-14T16:00:00.000Z",
        "budget_code": "PES-TRAINING-2025"
      }
    }
  ]
}
```

**Response Structure**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440002",
  "benefit_responses": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "timestamp": "2025-09-15T14:30:05.000Z",
      "status": "succ",
      "status_reason_code": "BENEFIT_ENROLLED",
      "data": {
        "benefit_id": "550e8400-e29b-41d4-a716-446655440003",
        "enrollment_number": "TRAIN-2025-001234",
        "start_date": "2025-10-01",
        "training_location": "Professional Institute ABC - Santiago Campus",
        "contact_person": "Maria Silva, Training Coordinator",
        "next_steps": [
          "attend_orientation_session",
          "complete_skills_assessment",
          "sign_participation_agreement"
        ]
      }
    }
  ]
}
```

---

## API.EMPL.04  PUT /job-placements/{placement_id}

**Purpose**: Update job placement status and outcomes for SP benefit management.

**Path Parameters**:
- `placement_id`: Unique placement identifier

**Request Structure**:
```json
{
  "placement_update": {
    "person_id": "person:12345",
    "placement_status": "successful",
    "start_date": "2025-10-22",
    "employment_details": {
      "employer_name": "Local Retail Chain SA",
      "job_title": "Sales Associate",
      "employment_type": "permanent_full_time",
      "monthly_salary": {
        "amount": "420000",
        "currency_code": "CLP"
      }
    },
    "follow_up_schedule": {
      "initial_check": "2025-11-22",
      "probation_review": "2026-01-22"
    },
    "sp_benefit_impact": {
      "benefit_continuation": false,
      "graduation_triggered": true
    },
    "updated_by": "caseworker:789",
    "update_reason": "employment_verification"
  }
}
```

**Response Structure**:
```json
{
  "placement_id": "550e8400-e29b-41d4-a716-446655440003",
  "status": "updated",
  "sp_notifications_sent": [
    {
      "target_system": "sp-mis",
      "notification_type": "benefit_graduation",
      "sent_at": "2025-09-15T14:30:10.000Z"
    }
  ],
  "next_review_date": "2025-11-22"
}
```

---

## API.EMPL.05  GET /compliance-monitoring

**Purpose**: Retrieve compliance status for employment obligations of SP beneficiaries.

**Query Parameters**:
- `person_id`: Person identifier (required)
- `monitoring_period_start`: ISO 8601 date
- `monitoring_period_end`: ISO 8601 date
- `obligation_types`: Comma-separated list of obligation types
- `compliance_status`: Filter by compliance status

**Response Structure**:
```json
{
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
      "compliance_rate": 1.0,
      "evidence_count": 6,
      "last_evidence_date": "2025-09-12"
    },
    {
      "obligation_type": "pes_appointments",
      "requirement": "monthly_caseworker_meeting",
      "status": "non_compliant",
      "compliance_rate": 0.5,
      "missed_appointments": 1,
      "next_appointment": "2025-10-15T10:00:00.000Z"
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
  "next_review_date": "2025-10-15"
}
```

---

## API.EMPL.06  POST /notifications

**Purpose**: Send notifications between PES and SP systems for status changes.

**Request Structure**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "notifications": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "notification_type": "employment_status_change",
      "person_id": "person:12345",
      "source_system": "pes",
      "target_system": "sp-mis",
      "notification_data": {
        "previous_status": "seeking_work",
        "new_status": "employed",
        "effective_date": "2025-10-22",
        "employment_details": {
          "employer_name": "Local Retail Chain SA",
          "monthly_income": {
            "amount": "420000",
            "currency_code": "CLP"
          }
        },
        "sp_action_required": "benefit_graduation_review"
      },
      "priority": "high",
      "expected_response": "acknowledgment"
    }
  ]
}
```

**Response Structure**:
```json
{
  "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
  "notification_responses": [
    {
      "reference_id": "550e8400-e29b-41d4-a716-446655440001",
      "status": "succ",
      "message": "Notification processed successfully",
      "sp_case_updated": true,
      "benefit_status_changed": true,
      "next_review_scheduled": "2025-11-15"
    }
  ]
}
```

---

## Security & Compliance

**Authentication**: All endpoints require OAuth2 JWT tokens with appropriate scopes:
- `referral:create` - Create employment referrals
- `employment:read` - Read employment status information
- `benefit:manage` - Create/update employment benefits
- `placement:update` - Update job placement information
- `compliance:read` - Access compliance monitoring data
- `notification:send` - Send cross-system notifications

**Audit Logging**: All API calls logged with:
- Actor identification (system + user)
- Purpose of use and legal basis
- Data elements accessed/modified
- Correlation and request identifiers

**Rate Limiting**:
- Standard tier: 1,000 requests/hour
- Batch operations: 100 items per request maximum
- Real-time notifications: 10,000 requests/hour

**Data Protection**:
- PII minimization enforced at API level
- Consent verification required for cross-system data sharing
- Retention policies applied automatically
- Cross-border data transfer compliance

---

## API.EMPL.07  GET /referrals

**Purpose**: Search and retrieve employment referrals by various criteria with pagination support.

**Query Parameters**:
- `person_id`: Person identifier (optional)
- `status`: Filter by referral status (optional) - [pending, accepted, rejected, completed]
- `source_programme`: Filter by source SP programme (optional)
- `created_from`: Filter referrals created from this date (ISO 8601, optional)
- `created_to`: Filter referrals created until this date (ISO 8601, optional)
- `limit`: Maximum items to return (default: 50, max: 500)
- `offset`: Number of items to skip for pagination (default: 0)

**Response Structure**:
```json
{
  "referrals": [
    {
      "referral_id": "550e8400-e29b-41d4-a716-446655440000",
      "person_id": "person:12345",
      "source_programme": "SP-CASH-2024",
      "referral_reason": "unemployment_benefit_activation",
      "target_pes_office": "PES-SANTIAGO-CENTRO",
      "status": "pending",
      "referral_date": "2025-09-15T14:30:00.000Z",
      "created_at": "2025-09-15T14:30:00.000Z"
    }
  ],
  "pagination": {
    "total_count": 1247,
    "limit": 50,
    "offset": 0,
    "has_more": true
  }
}
```

---

## API.EMPL.08  GET /employment-benefits

**Purpose**: Search employment benefits by person, type, or status with filtering and pagination.

**Query Parameters**:
- `person_id`: Person identifier (optional)
- `benefit_type`: Filter by benefit type (optional) - [training, work_opportunity, employment_subsidy]
- `status`: Filter by benefit status (optional) - [enrolled, active, completed, withdrawn, suspended]
- `start_date_from`: Filter benefits starting from this date (optional)
- `start_date_to`: Filter benefits starting until this date (optional)
- `limit`: Maximum items to return (default: 50, max: 500)
- `offset`: Number of items to skip for pagination (default: 0)

**Response Structure**:
```json
{
  "benefits": [
    {
      "benefit_id": "550e8400-e29b-41d4-a716-446655440002",
      "person_id": "person:12345",
      "benefit_type": "training",
      "employment_benefit_subtype": "vocational_skills_training",
      "benefit_description": "Digital Marketing Skills Training Program",
      "start_date": "2025-10-01",
      "end_date": "2025-12-20",
      "status": "enrolled",
      "completion_status": "enrolled"
    }
  ],
  "pagination": {
    "total_count": 89,
    "limit": 50,
    "offset": 0,
    "has_more": true
  }
}
```

---

## API.EMPL.09  GET /job-placements

**Purpose**: Search job placements by person, employer, or date range with comprehensive filtering.

**Query Parameters**:
- `person_id`: Person identifier (optional)
- `employer_name`: Filter by employer name (optional)
- `placement_from`: Filter placements from this date (optional)
- `placement_to`: Filter placements until this date (optional)
- `employment_type`: Filter by employment type (optional)
- `placement_method`: Filter by placement method (optional)
- `limit`: Maximum items to return (default: 50, max: 500)
- `offset`: Number of items to skip for pagination (default: 0)

**Response Structure**:
```json
{
  "placements": [
    {
      "placement_id": "550e8400-e29b-41d4-a716-446655440003",
      "person_id": "person:12345",
      "referral_id": "550e8400-e29b-41d4-a716-446655440000",
      "employer": {
        "employer_name": "Local Retail Chain SA",
        "sector": "retail",
        "company_size": "medium"
      },
      "job_details": {
        "job_title": "Sales Associate",
        "employment_type": "permanent_full_time"
      },
      "placement_date": "2025-10-15",
      "start_date": "2025-10-22",
      "placement_method": "pes_job_matching"
    }
  ],
  "pagination": {
    "total_count": 342,
    "limit": 50,
    "offset": 0,
    "has_more": true
  }
}
```

---

## API.EMPL.10  POST /webhooks/subscription

**Purpose**: Create webhook subscriptions for real-time notifications of employment-related events.

**Request Structure**:
```json
{
  "url": "https://sp-system.example.com/webhooks/employment-events",
  "event_types": [
    "referral_created",
    "referral_updated",
    "employment_status_changed",
    "benefit_enrolled",
    "benefit_completed",
    "placement_created",
    "placement_updated",
    "compliance_alert",
    "chile_rsh_classification_updated",
    "chile_sence_training_voucher_issued",
    "korea_work24_ai_match_found",
    "korea_mobile_job_diary_updated",
    "germany_bildungsgutschein_approved",
    "germany_sgb_compliance_violation",
    "australia_jobactive_outcome_achieved",
    "australia_mutual_obligation_missed",
    "turkey_isaf_beneficiary_status_changed"
  ],
  "filters": {
    "person_ids": ["person:12345", "person:67890"],
    "programmes": ["SP-CASH-2024", "SP-TRAINING-2024"],
    "country_implementation": ["CL-RSH-SENCE", "KR-WORK24"]
  },
  "secret": "webhook_signature_secret_key",
  "description": "SP-MIS employment event notifications with country-specific events"
}
```

**Response Structure**:
```json
{
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "url": "https://sp-system.example.com/webhooks/employment-events",
  "event_types": [
    "referral_created",
    "referral_updated",
    "employment_status_changed",
    "benefit_enrolled",
    "benefit_completed",
    "placement_created",
    "placement_updated",
    "compliance_alert"
  ],
  "status": "active",
  "created_at": "2025-09-15T14:30:00.000Z",
  "filters": {
    "person_ids": ["person:12345", "person:67890"],
    "programmes": ["SP-CASH-2024", "SP-TRAINING-2024"]
  }
}
```

---

## API.EMPL.11  GET /webhooks/subscription

**Purpose**: List active webhook subscriptions for the authenticated client.

**Query Parameters**:
- `event_type`: Filter by specific event type (optional)
- `status`: Filter by subscription status (optional) - [active, paused, disabled]
- `limit`: Maximum items to return (default: 50, max: 100)
- `offset`: Number of items to skip for pagination (default: 0)

**Response Structure**:
```json
{
  "subscriptions": [
    {
      "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
      "url": "https://sp-system.example.com/webhooks/employment-events",
      "event_types": ["referral_created", "employment_status_changed"],
      "status": "active",
      "created_at": "2025-09-15T14:30:00.000Z",
      "last_delivery": "2025-09-15T16:45:00.000Z",
      "delivery_stats": {
        "total_deliveries": 1247,
        "successful_deliveries": 1239,
        "failed_deliveries": 8,
        "last_success": "2025-09-15T16:45:00.000Z",
        "last_failure": "2025-09-14T08:22:00.000Z"
      }
    }
  ],
  "pagination": {
    "total_count": 3,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

---

## API.EMPL.12  PUT /webhooks/subscription/{subscription_id}

**Purpose**: Update webhook subscription configuration or status.

**Path Parameters**:
- `subscription_id`: Unique subscription identifier (UUID)

**Request Structure**:
```json
{
  "url": "https://updated-sp-system.example.com/webhooks/employment-events",
  "event_types": [
    "referral_created",
    "employment_status_changed",
    "placement_created"
  ],
  "status": "active",
  "filters": {
    "programmes": ["SP-CASH-2024"]
  },
  "description": "Updated SP-MIS employment notifications"
}
```

**Response Structure**:
```json
{
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "url": "https://updated-sp-system.example.com/webhooks/employment-events",
  "event_types": [
    "referral_created",
    "employment_status_changed",
    "placement_created"
  ],
  "status": "active",
  "updated_at": "2025-09-15T17:30:00.000Z",
  "filters": {
    "programmes": ["SP-CASH-2024"]
  }
}
```

---

## API.EMPL.13  DELETE /webhooks/subscription/{subscription_id}

**Purpose**: Delete a webhook subscription.

**Path Parameters**:
- `subscription_id`: Unique subscription identifier (UUID)

**Response**: 204 No Content (successful deletion)

---

## Webhook Event Delivery

### Webhook Payload Structure

All webhook events follow a consistent payload structure:

```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440006",
  "event_type": "referral_created",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "source_system": "pes",
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "data": {
    "referral_id": "550e8400-e29b-41d4-a716-446655440000",
    "person_id": "person:12345",
    "status": "pending",
    "source_programme": "SP-CASH-2024"
  },
  "metadata": {
    "correlation_id": "550e8400-e29b-41d4-a716-446655440007",
    "retry_count": 0
  }
}
```

### Webhook Security

**Headers (sent with each webhook)**:
- `X-Webhook-Signature`: HMAC-SHA256 signature for payload verification
- `X-Webhook-Event`: Event type for quick filtering
- `X-Webhook-Delivery`: Unique delivery attempt identifier
- `Content-Type`: application/json

**Signature Verification**:
```
signature = HMAC-SHA256(webhook_secret, request_body)
X-Webhook-Signature = "sha256=" + hex(signature)
```

### Delivery Guarantees

- **At-least-once delivery**: Events may be delivered multiple times
- **Retry Logic**: Exponential backoff for failed deliveries
- **Timeout**: 30-second timeout for webhook endpoint response
- **Success Criteria**: HTTP 2xx response indicates successful delivery

### Retry Schedule

1. Immediate delivery attempt
2. Retry after 1 minute
3. Retry after 5 minutes
4. Retry after 15 minutes
5. Retry after 1 hour
6. Retry after 6 hours
7. Final retry after 24 hours

After 7 failed attempts, the subscription may be automatically disabled.

### Country-Specific Webhook Events

**Chile RSH Classification Update**:
```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440008",
  "event_type": "chile_rsh_classification_updated",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "source_system": "RSH",
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "data": {
    "person_id": "person:12345",
    "rsh_classification": {
      "vulnerability_percentile": 45,
      "previous_percentile": 52,
      "classification_date": "2025-09-15",
      "employment_eligibility": "eligible_for_sence_referral"
    },
    "automatic_actions_triggered": [
      "sence_referral_created",
      "employment_subsidy_evaluation"
    ]
  },
  "country_implementation": "CL-RSH-SENCE"
}
```

**South Korea AI Job Match**:
```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440009",
  "event_type": "korea_work24_ai_match_found",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "source_system": "Work24",
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "data": {
    "person_id": "person:12345",
    "ai_match_results": {
      "job_id": "JOB-WORK24-789123",
      "match_confidence": 0.89,
      "employer_name": "Samsung Electronics",
      "position_title": "Customer Service Representative",
      "expected_retention_probability": 0.78
    },
    "ai_model_version": "worknet_v2.1",
    "recommended_actions": [
      "schedule_employer_introduction",
      "provide_interview_coaching"
    ]
  },
  "country_implementation": "KR-WORK24"
}
```

**Germany Bildungsgutschein Approval**:
```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440010",
  "event_type": "germany_bildungsgutschein_approved",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "source_system": "BA",
  "subscription_id": "550e8400-e29b-41d4-a716-446655440005",
  "data": {
    "person_id": "person:12345",
    "bildungsgutschein": {
      "voucher_id": "BG-2025-789456",
      "voucher_value": 12000,
      "currency": "EUR",
      "training_duration_max_months": 18,
      "azav_certification_required": true,
      "valid_until": "2026-09-15"
    },
    "training_categories": [
      "IT_skills",
      "digital_marketing",
      "project_management"
    ]
  },
  "country_implementation": "DE-SGB"
}
```

---

## Rate Limiting & Performance

### Rate Limits by Operation Type

**Standard API Operations**:
- 1,000 requests/hour per client
- 10 requests/second burst rate
- 100 items maximum per batch request

**Search Operations**:
- 500 requests/hour per client
- 5 requests/second burst rate
- 500 items maximum per response

**Webhook Management**:
- 100 requests/hour per client
- 2 requests/second burst rate
- 10 active subscriptions maximum per client

**Real-time Notifications**:
- 10,000 requests/hour for high-volume integrations
- Special rate limits for production SP-PES integrations

### Performance Targets

**Response Time SLAs**:
- Simple operations (GET single resource): < 200ms
- Search operations: < 1 second
- Batch operations: < 5 seconds
- Complex compliance queries: < 10 seconds

**Throughput Capacity**:
- 10,000 concurrent API requests
- 1 million webhook deliveries per day
- 100,000 real-time employment status checks per hour

### Country Performance Benchmarks (2024 Operational Data)

**Chile (RSH-SENCE Integration)**:
- **Referral Processing**: 18 hours average (target: < 24 hours)
- **Cross-System Consistency**: 98.7% data consistency across RSH-SENCE-SII
- **Job Placement Rate**: 67% within 6 months for referred beneficiaries
- **API Availability**: 99.4% uptime with ClaveÚnica integration
- **Total Beneficiaries**: 694,245 employment subsidy recipients

**South Korea (Work24 Platform)**:
- **Service Integration Score**: 89% cross-program coordination efficiency
- **AI Matching Performance**: 31% improvement in placement success
- **Employment Rate**: 71% within 6 months for program participants
- **Mobile Adoption**: 96% Job Diary adoption rate among beneficiaries
- **Annual Claims Processing**: 4.2 million employment insurance claims

**Germany (SGB Framework)**:
- **Compliance Rate**: 94% beneficiary compliance with requirements
- **Decision Time**: 2.3 days average for Bildungsgutschein approval
- **Training Effectiveness**: 76% employment within 6 months post-training
- **Fraud Detection**: 0.8% fraud rate (industry leading)
- **Annual Investment**: €1.2 billion in training benefits

**Australia (JobActive Network)**:
- **26-Week Outcomes**: 63% of providers achieve employment targets
- **Automation Rate**: 87% of compliance monitoring automated
- **Cost Efficiency**: 23% cost-per-outcome improvement since 2020
- **Provider Performance**: Outcome-based funding model with real-time tracking

**Performance SLA Recommendations**:
- **Processing Time**: < 24 hours for referrals (based on Chile benchmark)
- **Cross-System Consistency**: > 95% (based on operational experience)
- **API Availability**: > 99% uptime for production integrations
- **Employment Outcomes**: Target 60-70% placement rate within 6 months

---

## API Versioning Strategy

### Version Headers

All requests must include API version:
```
X-API-Version: 1.0.0
```

### Supported Versions

- **v1.0.0**: Current stable version (all endpoints)
- **v1.1.0**: Next version (backward compatible additions)
- **v2.0.0**: Future major version (breaking changes)

### Deprecation Policy

- **Notice Period**: 6 months advance notice for breaking changes
- **Support Period**: Previous major version supported for 12 months
- **Migration Window**: 6-month overlap for version migration

### Version-Specific Features

**v1.0.0 Features**:
- Core CRUD operations for all employment objects
- Basic search and filtering capabilities
- Standard webhook event types
- OAuth2 authentication

**v1.1.0 Planned Additions**:
- Advanced search with full-text capabilities
- Bulk update operations
- Enhanced webhook filtering
- GraphQL endpoint (optional)

---

## Open Issues

`TODO(specification)`: ✅ Complete OpenAPI 3.1 specification generated in annexes/openapi-specs.md.

`TODO(testing)`: Define test scenarios for cross-system integration validation, including webhook delivery testing.

`TODO(monitoring)`: Establish SLA requirements and monitoring thresholds for each API method with alerting rules.

`TODO(sdk)`: Generate client SDKs for major programming languages (Java, Python, JavaScript, C#).

`TODO(migration)`: Create migration guides for systems upgrading between API versions.