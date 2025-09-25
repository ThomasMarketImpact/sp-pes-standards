# Common Data Types & Formats

This document defines technical specifications for data types and formatting standards used across all DCI interoperability interfaces.

## DTF.COM.01  DateTime Format

**Description**: Standardized date and time representation following ISO 8601.

**Format specification**:
```
YYYY-MM-DDTHH:mm:ss.sssZ        (UTC)
YYYY-MM-DDTHH:mm:ss.sss�HHMM    (with timezone offset)
```

**Examples**:
```json
{
  "created_at": "2025-09-15T14:30:00.000Z",
  "processed_at": "2025-09-15T11:30:00.000-03:00",
  "birth_date": "1985-03-15"
}
```

**Implementation rules**:
- Always use UTC for system timestamps
- Include timezone offset for user-facing times
- Date-only fields use YYYY-MM-DD format
- Millisecond precision required for audit trails
- Time zone conversion handled at presentation layer

**Validation patterns**:
- Full datetime: `^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}\.\d{3}(Z|[+-]\d{2}:\d{2})$`
- Date only: `^\d{4}-\d{2}-\d{2}$`

---

## DTF.COM.02  Currency Format

**Description**: Monetary amount representation with precision and currency specification.

**Format specification**:
```json
{
  "amount": "1250.50",
  "currency_code": "USD",
  "precision": 2
}
```

**Precision rules by currency**:
| Currency | Code | Decimal Places | Example |
|----------|------|----------------|---------|
| US Dollar | USD | 2 | "1250.50" |
| Chilean Peso | CLP | 0 | "125000" |
| South Korean Won | KRW | 0 | "1250000" |
| Turkish Lira | TRY | 2 | "1250.50" |

**Implementation rules**:
- Amount stored as decimal string to avoid floating-point errors
- Currency code follows ISO 4217 standard
- Precision matches currency standard (never exceed)
- Negative amounts prefixed with minus sign: "-1250.50"
- No thousand separators in storage format

**Validation patterns**:
- Amount: `^-?\d+(\.\d{1,4})?$`
- Currency: `^[A-Z]{3}$`

---

## DTF.COM.03  Identifier Format

**Description**: Standardized identifier structure with type specification and validation.

**Format specification**:
```json
{
  "identifier_type": "national_id",
  "identifier_value": "12345678901",
  "issuing_authority": "National Registry",
  "verification_status": "verified"
}
```

**Special format rules**:
- **UUID v4**: `xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx` (where y is 8, 9, A, or B)
- **National ID**: Country-specific format validation
- **System ID**: Alphanumeric, no special characters except hyphens and underscores

**Validation patterns**:
- UUID v4: `^[0-9a-f]{8}-[0-9a-f]{4}-4[0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$`
- System ID: `^[a-zA-Z0-9_-]+$`

---

## DTF.COM.04  Address Structure

**Description**: Hierarchical address representation supporting international formats.

**Format specification**:
```json
{
  "address_line_1": "123 Main Street",
  "address_line_2": "Apartment 4B",
  "locality": "Springfield",
  "sub_region_code": "COUNTY001",
  "region_code": "IL",
  "postal_code": "62701",
  "country_code": "US"
}
```

**Field specifications**:
- **address_line_1**: Required, max 100 characters
- **address_line_2**: Optional, max 100 characters
- **locality**: City/town name, max 50 characters
- **region_code**: State/province code, ISO 3166-2 where available
- **country_code**: Required, ISO 3166-1 alpha-2
- **postal_code**: Country-specific format

**Country-specific examples**:
```json
// Chile
{
  "address_line_1": "Av. Libertador Bernardo O'Higgins 1449",
  "locality": "Santiago",
  "region_code": "RM",
  "country_code": "CL",
  "postal_code": "8320000"
}

// South Korea
{
  "address_line_1": "26 Sejong-daero, Jung-gu",
  "locality": "Seoul",
  "region_code": "11",
  "country_code": "KR",
  "postal_code": "04520"
}
```

---

## DTF.COM.05  JSON-LD Context Definitions

**Description**: Standardized JSON-LD context for semantic data interchange.

**Base context structure**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  }
}
```

**Type definitions**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "Person": "spdci:Person",
    "Address": "spdci:Address",
    "Identifier": "spdci:Identifier",
    "DateTime": {
      "@id": "xsd:dateTime",
      "@type": "xsd:dateTime"
    },
    "Date": {
      "@id": "xsd:date",
      "@type": "xsd:date"
    }
  }
}
```

**Usage rules**:
- Include @context in all JSON-LD documents
- Use consistent namespace prefixes across interfaces
- Extend base context for interface-specific terms
- Version contexts when breaking changes occur

---

## DTF.COM.06  API Request/Response Headers

**Description**: Standardized HTTP headers for API communication.

**Required request headers**:
```http
Authorization: Bearer <jwt-token>
Content-Type: application/json
X-Correlation-ID: 550e8400-e29b-41d4-a716-446655440000
X-Request-ID: 550e8400-e29b-41d4-a716-446655440001
X-API-Version: 1.0.0
Accept-Language: en-US,en;q=0.9
```

**Standard response headers**:
```http
Content-Type: application/json
X-Correlation-ID: 550e8400-e29b-41d4-a716-446655440000
X-Processing-Time: 150
X-Rate-Limit-Remaining: 999
X-Supported-Versions: 1.0.0,1.1.0
Cache-Control: no-store
```

**Header specifications**:
- **X-Correlation-ID**: UUID v4 for end-to-end tracing
- **X-Request-ID**: UUID v4 for individual request identification
- **X-Processing-Time**: Response time in milliseconds
- **X-API-Version**: Semantic version (MAJOR.MINOR.PATCH)
- **Accept-Language**: RFC 5646 language tags

---

## DTF.COM.07  Error Response Format

**Description**: Standardized error response structure for consistent error handling.

**Format specification**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "person.identifiers[0].identifier_value",
        "issue": "Required field missing",
        "value_provided": null
      }
    ],
    "correlation_id": "550e8400-e29b-41d4-a716-446655440000",
    "timestamp": "2025-09-15T14:30:00.000Z",
    "help_url": "https://docs.spdci.org/errors/validation"
  }
}
```

**Error detail structure**:
- **code**: Error category from `CD.COM.ErrorCategory`
- **message**: Human-readable error description
- **details**: Array of specific field-level errors
- **correlation_id**: Request correlation for debugging
- **help_url**: Documentation link for error resolution

---

## DTF.COM.08  Pagination Format

**Description**: Standardized pagination for list responses.

**Format specification**:
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "page_size": 50,
    "total_items": 1250,
    "total_pages": 25,
    "has_next": true,
    "has_previous": false,
    "next_url": "/api/v1/persons?page=2&page_size=50",
    "previous_url": null
  }
}
```

**Implementation rules**:
- Default page size: 50 items
- Maximum page size: 1000 items
- Page numbers start from 1
- Include total counts for client planning
- Provide navigation URLs for convenience

---

## DTF.COM.09  Search Query Format

**Description**: Standardized search parameter structure for consistent querying.

**Format specification**:
```json
{
  "filters": {
    "person.birth_date": {
      "gte": "1980-01-01",
      "lte": "1990-12-31"
    },
    "person.addresses.country_code": {
      "in": ["CL", "KR", "TR"]
    }
  },
  "sort": [
    {"field": "person.name.surname", "order": "asc"},
    {"field": "created_at", "order": "desc"}
  ],
  "pagination": {
    "page": 1,
    "page_size": 50
  }
}
```

**Filter operators**:
- **eq**: Equals (default if no operator specified)
- **ne**: Not equals
- **gt/gte**: Greater than / Greater than or equal
- **lt/lte**: Less than / Less than or equal
- **in**: Value in list
- **like**: Pattern matching (use % wildcards)

---

## DTF.COM.10  Audit Log Format

**Description**: Comprehensive audit record structure for compliance tracking.

**Format specification**:
```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440000",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "actor": {
    "type": "system",
    "id": "sp-mis-prod",
    "user_id": "user:12345"
  },
  "action": "read",
  "resource": {
    "type": "Person",
    "id": "person:67890",
    "path": "/api/v1/persons/67890"
  },
  "context": {
    "purpose": "benefit_eligibility_check",
    "legal_basis": "social_protection_law_2023",
    "correlation_id": "550e8400-e29b-41d4-a716-446655440001",
    "client_ip": "192.168.1.100",
    "user_agent": "SP-MIS/1.0"
  },
  "result": "success"
}
```

**Implementation rules**:
- Immutable records with cryptographic integrity
- All sensitive data access logged
- Purpose limitation enforced and documented
- Retention per legal requirements (minimum 7 years)

---

## Open Issues

`TODO(validation)`: Implement JSON Schema definitions for all data types.

`TODO(performance)`: Define compression standards for large payload optimization.