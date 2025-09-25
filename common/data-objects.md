# Common Data Objects

This document defines core data objects that are reusable across all DCI interoperability interfaces.

## DO.COM.01  Person

**Description**: A person entity representing an individual in social protection and employment systems.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "Person",
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
    "surname": "Rodriguez",
    "second_name": "Carmen"
  },
  "birth_date": "1985-03-15",
  "sex": "female",
  "addresses": [
    {
      "@type": "Address",
      "address_type": "residential",
      "address_line_1": "Calle Principal 123",
      "locality": "Santiago",
      "region_code": "RM",
      "country_code": "CL",
      "postal_code": "8320000"
    }
  ]
}
```

**Constraints**:
- At least one identifier MUST be provided
- birth_date MUST use ISO 8601 date format (YYYY-MM-DD)
- sex values MUST use `CD.COM.SexCategory` enumeration
- identifiers MUST reference `DO.COM.Identifier` objects

**Governance**:
- PII minimization: Only include fields necessary for the specific use case
- Retention per country-specific data protection requirements
- Access control: Person-level permissions required for sensitive attributes

---

## DO.COM.02  Address

**Description**: A structured address representation supporting international address formats.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "Address",
  "address_type": "residential",
  "address_line_1": "123 Main Street",
  "address_line_2": "Apartment 4B",
  "locality": "Springfield",
  "sub_region_code": "COUNTY001",
  "region_code": "IL",
  "postal_code": "62701",
  "country_code": "US",
  "geo_location": {
    "@type": "GeoLocation",
    "latitude": 39.7817,
    "longitude": -89.6501
  }
}
```

**Constraints**:
- address_line_1 and country_code MUST be provided
- country_code MUST use ISO 3166-1 alpha-2 format
- region_code SHOULD use ISO 3166-2 subdivision codes where applicable
- geo_location is optional but recommended for service delivery optimization

**Governance**:
- Address verification recommended but not required
- Geocoding for service delivery planning (with privacy considerations)
- Historical addresses retained for benefit continuity tracking

---

## DO.COM.03  Identifier

**Description**: A standardized identifier structure for unique entity identification.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "Identifier",
  "identifier_type": "national_id",
  "identifier_value": "12345678901",
  "issuing_authority": "National Registry",
  "issue_date": "2010-01-15",
  "expiry_date": "2030-01-15",
  "verification_status": "verified"
}
```

**Constraints**:
- identifier_type MUST use values from `CD.COM.IdentifierTypes`
- identifier_value MUST be non-empty string
- UUID-based identifiers MUST use UUID v4 format
- Date fields MUST use ISO 8601 date format

**Governance**:
- Cross-system identifier mapping maintained in secure registries
- Verification status tracked for data quality assurance
- Legacy identifier migration supported with audit trails

---

## DO.COM.04  RequestStatus

**Description**: Standardized status enumeration for tracking request lifecycle.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "RequestStatus",
  "status_code": "pdng",
  "status_description": "Request pending processing",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "processing_notes": "Awaiting verification from external system"
}
```

**Status enumeration**:
- `rcvd`: Received - Request received by system
- `pdng`: Pending - Request initiated and under processing
- `succ`: Success - Request completed successfully
- `rjct`: Rejected - Request rejected due to validation or business rule failure

**Constraints**:
- status_code MUST use values from `CD.COM.RequestStatus`
- timestamp MUST use ISO 8601 UTC format
- processing_notes optional but recommended for non-success states

**Governance**:
- Status transitions logged for audit and monitoring
- SLA tracking based on status timestamps
- Error details captured for rejected requests

---

## DO.COM.05  Currency

**Description**: Monetary amount representation with currency specification.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "Currency",
  "amount": "1250.50",
  "currency_code": "USD",
  "precision": 2,
  "payment_date": "2025-09-15",
  "exchange_rate": "1.0000"
}
```

**Constraints**:
- amount MUST be decimal string with appropriate precision
- currency_code MUST use ISO 4217 three-letter codes
- precision MUST match currency standard (e.g., 2 for USD, 0 for JPY)
- exchange_rate relative to base currency (USD) for multi-currency operations

**Governance**:
- Currency conversion rates updated daily from authoritative sources
- Historical exchange rates maintained for audit purposes
- Rounding rules defined per currency and use case

---

## DO.COM.06  Name

**Description**: Structured name representation supporting international naming conventions.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "Name",
  "given_name": "Ahmed",
  "surname": "Al-Rahman",
  "second_name": "Mohammed",
  "maiden_name": "Al-Zahra",
  "prefix": "Dr.",
  "suffix": "Jr.",
  "full_name": "Dr. Ahmed Mohammed Al-Rahman Jr."
}
```

**Constraints**:
- At least given_name OR surname MUST be provided
- full_name constructed from components when not explicitly provided
- Cultural naming conventions supported through flexible field structure
- Name verification optional but recommended for identity confirmation

**Governance**:
- Name changes tracked with effective dates and documentation
- Transliteration standards applied for cross-system compatibility
- Privacy protection for sensitive name components (e.g., maiden names)

---

## DO.COM.07  AuditEvent

**Description**: Comprehensive audit record for data processing activities.

**Fields (JSON-LD sketch)**:
```json
{
  "@context": {
    "@vocab": "http://spdci.org/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "schema": "http://schema.org/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "owl": "http://www.w3.org/2002/07/owl#"
  },
  "@type": "AuditEvent",
  "event_id": "550e8400-e29b-41d4-a716-446655440000",
  "timestamp": "2025-09-15T14:30:00.000Z",
  "actor_id": "system:sp-mis",
  "action": "data_access",
  "resource_type": "Person",
  "resource_id": "person:12345",
  "purpose": "benefit_eligibility_check",
  "legal_basis": "social_protection_law_2023",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440001",
  "client_ip": "192.168.1.100",
  "user_agent": "SP-MIS/1.0"
}
```

**Constraints**:
- event_id MUST be UUID v4 format
- timestamp MUST be ISO 8601 UTC format
- purpose MUST align with documented use cases
- legal_basis MUST reference applicable law or regulation

**Governance**:
- Immutable audit records with cryptographic integrity
- Retention period: 7 years minimum or per legal requirement
- Regular audit compliance reporting and anomaly detection

---

## Open Issues

`TODO(evidence)`: Validate data object structures against country-specific requirements (Chile, South Korea, Turkey, South Africa).

`TODO(schema)`: Generate formal JSON-LD context definitions for all common data objects.