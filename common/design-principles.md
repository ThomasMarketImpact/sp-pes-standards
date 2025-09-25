# Common Design Principles

This document defines API design principles and security standards that apply across all DCI interoperability interfaces.

## API.COM.01  OAuth2 Security Framework

**Principle**: All API access requires OAuth2 authentication with standardized token management.

**Implementation requirements**:
- **Grant type**: Client Credentials flow for machine-to-machine communication
- **Token format**: JWT (JSON Web Token) with standardized claims
- **Token lifetime**: 1 hour maximum with refresh capability
- **Scope limitation**: Fine-grained permissions per API endpoint

**JWT token structure**:
```json
{
  "iss": "https://auth.spdci.org",
  "aud": "employment-sp-api",
  "sub": "client:sp-mis-system",
  "scope": "person:read referral:create",
  "iat": 1694781000,
  "exp": 1694784600,
  "purpose": "benefit_eligibility_verification"
}
```

**Security requirements**:
- TLS 1.2+ for all communications
- Token storage in secure key management systems
- Regular token rotation (at least daily)
- Audit logging of all authentication events

**Error handling**:
- 401 Unauthorized: Invalid or expired token
- 403 Forbidden: Valid token, insufficient scope
- Rate limiting: 1000 requests per hour per client

---

## API.COM.02  Standard HTTP Headers

**Principle**: Consistent header usage ensures traceability, versioning, and proper content negotiation.

**Required request headers**:
```http
Authorization: Bearer <jwt-token>
Content-Type: application/json
X-Correlation-ID: <uuid-v4>
X-Request-ID: <uuid-v4>
X-API-Version: 1.0.0
Accept-Language: en-US,en;q=0.9
```

**Standard response headers**:
```http
Content-Type: application/json
X-Correlation-ID: <same-as-request>
X-Processing-Time: <milliseconds>
X-Rate-Limit-Remaining: <count>
X-Supported-Versions: 1.0.0,1.1.0
Cache-Control: no-store
```

**Header specifications**:
- **X-Correlation-ID**: End-to-end request tracking (UUID v4)
- **X-Request-ID**: Individual API call identification (UUID v4)
- **X-API-Version**: Semantic versioning (MAJOR.MINOR.PATCH)
- **X-Processing-Time**: Server processing time in milliseconds
- **Accept-Language**: RFC 5646 language preference

**Cache control**:
- Personal data responses: `Cache-Control: no-store, no-cache`
- Public reference data: `Cache-Control: public, max-age=3600`
- Rate limit information: Always included in responses

---

## API.COM.03  RESTful Resource Design

**Principle**: APIs follow REST conventions with consistent resource naming and HTTP method usage.

**Resource naming conventions**:
```
/api/v1/persons                    # Collection
/api/v1/persons/{person_id}        # Individual resource
/api/v1/persons/{person_id}/referrals  # Sub-resource collection
/api/v1/referrals/{referral_id}    # Direct resource access
```

**HTTP method usage**:
- **GET**: Retrieve resources (idempotent, cacheable)
- **POST**: Create new resources (non-idempotent)
- **PUT**: Replace entire resource (idempotent)
- **PATCH**: Partial resource update (idempotent)
- **DELETE**: Remove resource (idempotent)

**Response status codes**:
- **200 OK**: Successful GET, PUT, PATCH
- **201 Created**: Successful POST with new resource
- **204 No Content**: Successful DELETE
- **400 Bad Request**: Validation errors
- **404 Not Found**: Resource doesn't exist
- **409 Conflict**: Resource state conflict

**Resource representation**:
- Use JSON-LD for semantic interoperability
- Include resource links for navigation (HATEOAS principles)
- Consistent date/time formatting (ISO 8601)
- Proper HTTP status codes and error responses

---

## API.COM.04  Error Response Standards

**Principle**: Standardized error responses enable consistent client error handling and debugging.

**Error response structure**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "person.identifiers[0].identifier_value",
        "issue": "Required field missing",
        "value_provided": null,
        "constraints": {
          "required": true,
          "format": "non-empty string"
        }
      }
    ],
    "correlation_id": "550e8400-e29b-41d4-a716-446655440000",
    "timestamp": "2025-09-15T14:30:00.000Z",
    "help_url": "https://docs.spdci.org/errors/validation",
    "retry_after": null
  }
}
```

**Error categories** (from `CD.COM.ErrorCategory`):
- **Client errors** (4xx): Validation, authentication, authorization
- **Server errors** (5xx): Internal errors, service unavailable
- **Rate limiting** (429): Request throttling with retry guidance

**Error detail requirements**:
- Field-level validation errors with specific constraints
- Correlation ID for request tracing
- Documentation links for error resolution
- Retry guidance for recoverable errors

---

## API.COM.05  API Versioning Strategy

**Principle**: Semantic versioning enables controlled API evolution while maintaining backward compatibility.

**Versioning scheme**:
- **MAJOR.MINOR.PATCH** (e.g., 1.2.3)
- **MAJOR**: Breaking changes (remove fields, change types)
- **MINOR**: Backward-compatible additions (new fields, endpoints)
- **PATCH**: Bug fixes, documentation updates

**Version communication**:
```http
# Request header
X-API-Version: 1.0.0

# Response headers
X-Supported-Versions: 1.0.0,1.1.0,2.0.0
X-Deprecated-Versions: 0.9.0
```

**Backward compatibility**:
- Support minimum 2 major versions simultaneously
- 6-month deprecation notice for breaking changes
- Clear migration guides for version upgrades
- Automatic content negotiation based on client version

**Breaking change examples**:
- Removing required fields
- Changing field data types
- Modifying endpoint URLs
- Changing authentication requirements

---

## API.COM.06  Rate Limiting and Throttling

**Principle**: Protect API availability through fair usage policies and abuse prevention.

**Rate limiting tiers**:
```
Standard:    1,000 requests/hour per client
Premium:     10,000 requests/hour per client
Burst:       100 requests/minute (short-term)
```

**Rate limit headers**:
```http
X-Rate-Limit-Limit: 1000
X-Rate-Limit-Remaining: 995
X-Rate-Limit-Reset: 1694784600
X-Rate-Limit-Window: 3600
```

**Throttling behavior**:
- 429 Too Many Requests when limit exceeded
- Exponential backoff recommendations in Retry-After header
- Different limits for read vs. write operations
- Temporary increases available for legitimate bulk operations

**Implementation considerations**:
- Rate limits applied per OAuth2 client_id
- Separate quotas for different API endpoints
- Grace period for first-time quota violations
- Monitoring and alerting for unusual usage patterns

---

## API.COM.07  Content Negotiation and Localization

**Principle**: Support multiple languages and content formats for global accessibility.

**Content type negotiation**:
```http
Accept: application/json, application/ld+json
Content-Type: application/json; charset=utf-8
```

**Language negotiation**:
```http
Accept-Language: es-CL,es;q=0.9,en;q=0.8
Content-Language: es-CL
```

**Supported formats**:
- **application/json**: Standard API responses
- **application/ld+json**: JSON-LD for semantic interoperability
- **text/csv**: Bulk data export (where applicable)

**Localization strategy**:
- Error messages translated to user's preferred language
- Reference data (enumerations) available in multiple languages
- Date/number formatting follows locale conventions
- Fallback hierarchy: requested → country default → English

---

## API.COM.08  Data Validation and Schema Enforcement

**Principle**: Rigorous input validation ensures data quality and system integrity.

**Validation layers**:
1. **Schema validation**: JSON Schema enforcement
2. **Business rule validation**: Domain-specific constraints
3. **Cross-field validation**: Relationship consistency
4. **External validation**: Third-party data verification

**Validation response example**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Multiple validation errors found",
    "details": [
      {
        "field": "person.birth_date",
        "issue": "Date cannot be in the future",
        "value_provided": "2030-01-01",
        "constraints": {
          "max_date": "2025-09-15"
        }
      }
    ]
  }
}
```

**Schema evolution**:
- New optional fields: Non-breaking change
- New required fields: Breaking change requiring version increment
- Field removal: Breaking change with deprecation period
- Format changes: Breaking change with migration support

---

## API.COM.09  Audit and Compliance Logging

**Principle**: Comprehensive audit trails support regulatory compliance and security monitoring.

**Audit requirements**:
- All personal data access logged with purpose
- Request/response correlation maintained
- Actor identification (user, system, client)
- Legal basis documentation for data processing

**Audit log structure**:
```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440000",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "event_type": "api_request",
  "actor": {
    "type": "system",
    "id": "sp-mis-prod",
    "user_id": "caseworker:12345"
  },
  "action": "GET /api/v1/persons/67890",
  "resource": {
    "type": "Person",
    "id": "person:67890",
    "sensitive_fields": ["identifiers", "addresses"]
  },
  "context": {
    "purpose": "benefit_eligibility_check",
    "legal_basis": "social_protection_law_2023",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440001"
  },
  "result": {
    "status": "success",
    "http_status": 200,
    "processing_time_ms": 150
  }
}
```

**Retention policies**:
- Personal data access logs: 7 years minimum
- System performance logs: 1 year
- Security events: 10 years
- Compliance reports: Per regulatory requirements

---

## API.COM.10  Performance and Monitoring Standards

**Principle**: Proactive monitoring ensures reliable service delivery and optimal user experience.

**Performance targets**:
- **Response time**: 95th percentile < 500ms for simple queries
- **Availability**: 99.9% uptime (8.76 hours downtime/year)
- **Throughput**: 1000 concurrent requests per service instance
- **Data consistency**: Eventual consistency within 30 seconds

**Monitoring requirements**:
```json
{
  "metrics": {
    "response_time_p95": 350,
    "error_rate": 0.001,
    "throughput_rps": 850,
    "availability": 0.9995
  },
  "alerts": {
    "response_time_threshold": 1000,
    "error_rate_threshold": 0.01,
    "availability_threshold": 0.999
  }
}
```

**Health check endpoints**:
- **GET /health**: Basic service health
- **GET /health/detailed**: Dependency status, resource usage
- **GET /metrics**: Prometheus-compatible metrics
- **GET /version**: API version and build information

**Observability stack**:
- Request tracing with correlation IDs
- Application performance monitoring (APM)
- Infrastructure monitoring and alerting
- Business metrics tracking (usage patterns, feature adoption)

---

## API.COM.11 — DCI Interface Integration Patterns

**Principle**: Employment-SP APIs follow standardized DCI patterns for cross-interface coordination and ecosystem integration.

### **Cross-Interface Data Exchange**

**Standard DCI Message Structure**:
```json
{
  "signature": "eyJhbGciOiJSUzI1NiIs...",
  "header": {
    "version": "1.0.0",
    "message_id": "550e8400-e29b-41d4-a716-446655440000",
    "message_ts": "2025-09-22T10:00:00.000Z",
    "action": "employment_referral",
    "sender_id": "SOCIAL-REGISTRY-CL",
    "receiver_id": "PES-SENCE-CL",
    "total_count": "1",
    "is_msg_encrypted": false
  },
  "message": {
    "@context": {
      "@vocab": "http://spdci.org/",
      "xsd": "http://www.w3.org/2001/XMLSchema#",
      "schema": "http://schema.org/"
    },
    "transaction_id": "550e8400-e29b-41d4-a716-446655440001",
    "employment_referral": [
      { /* Interface-specific payload */ }
    ]
  }
}
```

### **DCI Standard Endpoints**

All DCI interfaces implement consistent endpoint patterns:

| Pattern | Employment-SP Implementation | Purpose |
|---------|------------------------------|---------|
| `POST /{interface}/search` | `POST /employment/search` | Person/entity discovery |
| `POST /{interface}/subscribe` | `POST /employment/subscribe` | Event notifications |
| `GET /{interface}/status/{id}` | `GET /employment/status/{id}` | Request tracking |
| `POST /{interface}/notify` | `POST /employment/notify` | Cross-system events |

### **Government Interoperability Platform Integration**

**Platform-Mediated Architecture**:
- All DCI interfaces connect through central platform
- Platform handles authentication, routing, and transformation
- Unified audit logging and compliance monitoring
- Consistent error handling and retry mechanisms

**Direct Integration Support**:
- Bilateral connections for high-frequency operations
- Point-to-point integration for real-time requirements
- Fallback to platform routing for resilience

### **Cross-Interface Identity Resolution**

**Person Identification Across DCI Interfaces**:
```json
{
  "person_lookup": {
    "identifiers": [
      {
        "identifier_type": "national_id",
        "identifier_value": "12345678901",
        "issuing_authority": "civil_registry"
      }
    ],
    "resolution_scope": [
      "social_registry",
      "ibr",
      "disability_registry"
    ]
  }
}
```

### **Event-Driven Coordination**

**Cross-Interface Event Patterns**:
- **Employment Status Change**: Notify IBR, Social Registry, Payment Systems
- **Benefit Transition**: Coordinate between SP and employment services
- **Training Completion**: Update skills profiles across relevant registries
- **Compliance Violation**: Trigger interventions across coordinated services

**Event Subscription Management**:
```json
{
  "subscription": {
    "subscriber_id": "EMPLOYMENT-SP-CL",
    "event_types": [
      "person.socioeconomic_status.changed",
      "person.work_capacity.assessed",
      "benefit.status.terminated"
    ],
    "delivery_endpoint": "https://employment.gov.cl/api/v1/events",
    "filter_criteria": {
      "person.addresses.region": "santiago"
    }
  }
}
```

### **Implementation Quality Gates**

**DCI Compliance Verification**:
- JSON-LD schema validation against DCI standards
- Cross-interface data compatibility testing
- Authentication pattern compliance
- Message structure verification

**Performance Standards**:
- Cross-interface response time: < 5 seconds
- Data consistency: > 99%
- Authentication success: > 99.5%
- Event delivery reliability: > 99.9%

---

## Open Issues

`TODO(security)`: Define additional security headers (CSP, HSTS) for web-facing endpoints.

`TODO(performance)`: Establish SLA definitions and penalty frameworks for service degradation.