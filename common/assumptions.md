# Common Assumptions

This document outlines the foundational assumptions that apply across all DCI interoperability interfaces, including Employment-SP integration.

## ASM.COM.01  Data Minimization Principle

**Assumption**: Only data attributes necessary for the specific use case shall be shared between systems.

**Rationale**: Privacy protection and compliance with data protection regulations require limiting data exposure to the minimum necessary for functional requirements.

**Implementation**:
- APIs must document the specific purpose for each data field requested
- Receiving systems must not store or process data beyond stated purposes
- Data retention policies must align with legal requirements and functional needs

---

## ASM.COM.02  Unique Identifier Standards

**Assumption**: All entities require globally unique identifiers using standardized formats.

**Rationale**: Consistent identification enables reliable cross-system referencing and prevents data duplication.

**Implementation**:
- Primary keys use UUID v4 format for global uniqueness
- Country-specific identifiers (national ID, tax number) follow ISO standards where available
- Identifier types are standardized via `CD.COM.IdentifierTypes`

---

## ASM.COM.03  Authentication and Authorization

**Assumption**: All API access requires OAuth2 authentication with JWT bearer tokens.

**Rationale**: Standardized security protocols ensure consistent access control and audit capabilities across all DCI interfaces.

**Implementation**:
- Client credentials flow for machine-to-machine communication
- Access tokens must include scope limitations for specific operations
- Token expiration and refresh patterns follow OAuth2 specifications
- All requests must include purpose-of-use in audit logs

---

## ASM.COM.04  Message Correlation and Traceability

**Assumption**: All API requests must include correlation identifiers for end-to-end traceability.

**Rationale**: Distributed system debugging and audit requirements necessitate request tracking across multiple system boundaries.

**Implementation**:
- `X-Correlation-ID` header required for all requests
- `X-Request-ID` header for individual API call identification
- Processing time tracking via `X-Processing-Time` response header
- Audit logs must capture correlation chains

---

## ASM.COM.05  Standardized Status Reporting

**Assumption**: All asynchronous operations use common status enumeration patterns.

**Rationale**: Consistent status reporting enables standardized monitoring and error handling across interfaces.

**Implementation**:
- Request status follows `CD.COM.RequestStatus` enumeration
- Status transitions follow defined state machine patterns
- Error conditions include structured error codes and descriptions
- Status updates trigger notification events where applicable

---

## ASM.COM.06  Temporal Data Handling

**Assumption**: All timestamp data uses ISO 8601 format with timezone specification.

**Rationale**: Global interoperability requires unambiguous temporal data representation.

**Implementation**:
- DateTime format: `YYYY-MM-DDTHH:mm:ss.sssZ` (UTC) or `±HHMM` offset
- Event timestamps capture both creation and processing times
- Retention periods specified in human-readable and machine-processable formats

---

## ASM.COM.07  Multi-language and Localization

**Assumption**: Systems support multiple languages for human-readable content using ISO standards.

**Rationale**: Social protection systems serve diverse populations requiring localized interfaces and documentation.

**Implementation**:
- Language codes follow ISO 639-1/639-3 standards
- `Accept-Language` header indicates client language preferences
- Error messages and user-facing content support localization
- Country-specific data formats (addresses, names) use local conventions

---

## ASM.COM.08  Legal Basis and Consent Management

**Assumption**: All data sharing requires explicit legal basis documentation and, where applicable, individual consent.

**Rationale**: Data protection laws mandate clear legal justification for personal data processing and sharing.

**Implementation**:
- Purpose limitation documented for each data exchange
- Legal basis recorded in audit logs (law, consent, legitimate interest)
- Consent withdrawal mechanisms available where required
- Cross-border data transfer agreements documented

---

## ASM.COM.09 — DCI Ecosystem Integration

**Assumption**: Employment-SP implementations operate within the broader DCI ecosystem of interoperable social protection interfaces.

**Rationale**: The Employment-SP interface is designed as the 8th interface in the DCI architecture, requiring coordination with existing interfaces (CRVS, IBR, Social Registry, Disability Registry, Farmer Registry, ID Systems, Payment Systems).

**Implementation**:
- Cross-interface person identification using consistent identifier standards
- Event-driven coordination for benefit transitions and status changes
- Government Interoperability Platform integration where available
- Shared authentication and security patterns across all DCI interfaces
- JSON-LD schema compatibility for semantic interoperability

**DCI Interface Dependencies**:
- **Social Registry**: Work capacity assessments and referral generation
- **IBR**: Benefit enrollment coordination and status synchronization
- **Payment Systems**: Employment subsidies and training allowance coordination
- **CRVS**: Identity verification for employment eligibility
- **Disability Registry**: Reasonable accommodation requirements

**Implementation Approaches**:
- **Single Interface**: Employment-SP as standalone implementation with DCI-compatible patterns
- **Multi-Interface**: Coordinated deployment with existing DCI interfaces
- **Platform-Mediated**: Integration through Government Interoperability Platform

---

## Open Issues

`TODO(evidence)`: Validate assumption alignment with specific country legal frameworks (Chile, South Korea, Turkey, South Africa).

`TODO(legal)`: Review consent management requirements for Employment-SP specific use cases.