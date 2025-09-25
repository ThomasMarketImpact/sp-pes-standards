# Common Code Directories

This document defines standard enumerations and code lists used across all DCI interoperability interfaces.

## CD.COM.01  RequestStatus

**Description**: Standardized status codes for tracking request lifecycle across all interfaces.

**Enumeration**:
| Code | Description | Usage |
|------|-------------|-------|
| `rcvd` | Received | Request received by system and initial validation passed |
| `pdng` | Pending | Request initiated and under processing |
| `succ` | Success | Request completed successfully |
| `rjct` | Rejected | Request rejected due to validation or business rule failure |

**Usage notes**:
- Status transitions MUST follow logical sequence: rcvd ’ pdng ’ (succ|rjct)
- Each status change MUST be timestamped for SLA tracking
- Error details required for `rjct` status

---

## CD.COM.02  IdentifierTypes

**Description**: Standardized identifier types for person and entity identification.

**Enumeration**:
| Code | Description | Format | Country Scope |
|------|-------------|--------|---------------|
| `national_id` | National Identity Number | Country-specific | National |
| `passport` | Passport Number | Country-specific | International |
| `tax_id` | Tax Identification Number | Country-specific | National |
| `social_security` | Social Security Number | Country-specific | National |
| `driving_license` | Driver's License Number | Country-specific | National |
| `birth_certificate` | Birth Certificate Number | Country-specific | National |
| `uuid` | Universal Unique Identifier | UUID v4 format | Global |
| `system_id` | System-specific Identifier | System-defined | System |

**Usage notes**:
- Multiple identifiers allowed per person for cross-system linking
- Verification status should be tracked for each identifier
- Legacy identifier migration supported with mapping tables

---

## CD.COM.03  CurrencyCode

**Description**: Currency codes following ISO 4217 standard for monetary transactions.

**Common codes**:
| Code | Currency | Country/Region | Decimal Places |
|------|----------|----------------|----------------|
| `USD` | US Dollar | United States | 2 |
| `EUR` | Euro | European Union | 2 |
| `CLP` | Chilean Peso | Chile | 0 |
| `KRW` | South Korean Won | South Korea | 0 |
| `TRY` | Turkish Lira | Turkey | 2 |
| `ZAR` | South African Rand | South Africa | 2 |
| `BRL` | Brazilian Real | Brazil | 2 |
| `UYU` | Uruguayan Peso | Uruguay | 2 |

**Usage notes**:
- Complete ISO 4217 list maintained in system configuration
- Exchange rates updated daily from authoritative sources
- Historical rates retained for audit and reporting purposes

---

## CD.COM.04  CountryCode

**Description**: Country codes following ISO 3166-1 alpha-2 standard.

**Common codes**:
| Code | Country | Region |
|------|---------|--------|
| `CL` | Chile | South America |
| `KR` | South Korea | East Asia |
| `TR` | Turkey | Western Asia/Europe |
| `ZA` | South Africa | Southern Africa |
| `BR` | Brazil | South America |
| `UY` | Uruguay | South America |
| `XK` | Kosovo | Southeast Europe |
| `PH` | Philippines | Southeast Asia |
| `US` | United States | North America |

**Usage notes**:
- Complete ISO 3166-1 list maintained in system configuration
- Used for address validation and jurisdiction determination
- Regional groupings available for policy application

---

## CD.COM.05  LanguageCode

**Description**: Language codes following ISO 639-1/639-3 standards for internationalization.

**Common codes**:
| Code | Language | Native Name | Usage Context |
|------|----------|-------------|---------------|
| `en` | English | English | Global |
| `es` | Spanish | Español | Latin America, Spain |
| `pt` | Portuguese | Português | Brazil, Portugal |
| `ko` | Korean | \m´ | South Korea |
| `tr` | Turkish | Türkçe | Turkey |
| `af` | Afrikaans | Afrikaans | South Africa |
| `zu` | Zulu | isiZulu | South Africa |
| `xh` | Xhosa | isiXhosa | South Africa |

**Usage notes**:
- Accept-Language header indicates client preferences
- Fallback hierarchy: requested ’ national ’ English
- Error messages and documentation support localization

---

## CD.COM.06  SexCategory

**Description**: Standardized sex/gender categories following international guidelines.

**Enumeration**:
| Code | Description | Usage |
|------|-------------|-------|
| `male` | Male | Biological/legal designation |
| `female` | Female | Biological/legal designation |
| `other` | Other/Non-binary | Alternative designation |
| `unknown` | Unknown/Not specified | Data unavailable |

**Usage notes**:
- Follows local legal frameworks where applicable
- Privacy protection for sensitive demographic data
- Statistical reporting aggregation supported

---

## CD.COM.07  AddressType

**Description**: Address type classifications for different address uses.

**Enumeration**:
| Code | Description | Usage |
|------|-------------|-------|
| `residential` | Primary residence | Service delivery, correspondence |
| `mailing` | Mailing address | Document delivery |
| `work` | Employment address | Employment verification |
| `temporary` | Temporary address | Seasonal or short-term |
| `previous` | Previous address | Historical tracking |

**Usage notes**:
- Multiple addresses allowed per person with type designation
- Address verification recommended for service delivery
- Historical addresses retained for benefit continuity

---

## CD.COM.08  VerificationStatus

**Description**: Data verification status for quality assurance.

**Enumeration**:
| Code | Description | Verification Method |
|------|-------------|-------------------|
| `verified` | Verified against authoritative source | Document check, system integration |
| `self_declared` | Self-declared by individual | User attestation |
| `pending` | Verification in progress | Awaiting external confirmation |
| `failed` | Verification failed | Discrepancy found |
| `expired` | Verification expired | Reverification required |

**Usage notes**:
- Verification timestamp and method recorded
- Regular reverification cycles based on data sensitivity
- Failed verification triggers data quality alerts

---

## CD.COM.09  AuditAction

**Description**: Standardized audit action types for compliance tracking.

**Enumeration**:
| Code | Description | Data Impact |
|------|-------------|-------------|
| `create` | Record creation | New data |
| `read` | Data access | No change |
| `update` | Record modification | Data change |
| `delete` | Record deletion | Data removal |
| `search` | Search operation | Query execution |
| `export` | Data export | Bulk data access |
| `import` | Data import | Bulk data creation |
| `consent` | Consent management | Privacy control |

**Usage notes**:
- All actions logged with timestamp and actor
- Purpose of use documented for each action
- Retention periods vary by action type and data classification

---

## CD.COM.10  ErrorCategory

**Description**: Error classification for standardized error handling.

**Enumeration**:
| Code | Description | HTTP Status | Retry Behavior |
|------|-------------|-------------|----------------|
| `VALIDATION_ERROR` | Request validation failed | 400 | No retry |
| `AUTHENTICATION_ERROR` | Authentication failed | 401 | No retry |
| `AUTHORIZATION_ERROR` | Authorization denied | 403 | No retry |
| `NOT_FOUND` | Resource not found | 404 | No retry |
| `CONFLICT` | Resource conflict | 409 | No retry |
| `RATE_LIMIT` | Rate limit exceeded | 429 | Exponential backoff |
| `SERVER_ERROR` | Internal server error | 500 | Exponential backoff |
| `SERVICE_UNAVAILABLE` | Service temporarily unavailable | 503 | Exponential backoff |

**Usage notes**:
- Structured error details provided for debugging
- Client retry behavior standardized by category
- Error monitoring and alerting based on categories

---

## Open Issues

`TODO(evidence)`: Validate enumeration values against country-specific legal and regulatory requirements.

`TODO(localization)`: Complete translation matrices for multi-language support in target countries.