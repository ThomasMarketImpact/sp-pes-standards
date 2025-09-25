# OpenAPI 3.1 Specifications

This document contains the complete OpenAPI specifications for the Employment-SP Interoperability Standards, following DCI patterns and semantic data exchange principles.

## Interactive API Documentation

### Option 1: Redoc Documentation Browser

<div id="redoc-container"></div>
<script src="https://cdn.jsdelivr.net/npm/redoc@2.1.3/bundles/redoc.standalone.js"></script>
<script>
  // Extract YAML from the current document
  const yamlContent = document.querySelector('pre code.language-yaml')?.textContent ||
                     document.querySelector('code[class*="yaml"]')?.textContent ||
                     document.body.textContent.match(/```yaml\n([\s\S]*?)\n```/)?.[1];

  if (yamlContent) {
    try {
      // Convert YAML to JSON for Redoc
      const spec = jsyaml.load(yamlContent);
      Redoc.init(spec, {
        theme: {
          colors: {
            primary: { main: '#1976d2' }
          },
          typography: {
            fontSize: '14px',
            lineHeight: '1.5em',
            fontFamily: '"Roboto", sans-serif'
          }
        },
        expandResponses: "200,201",
        requiredPropsFirst: true,
        noAutoAuth: false,
        pathInMiddlePanel: true,
        hideHostname: false,
        expandDefaultServerVariables: true,
        showExtensions: true
      }, document.getElementById('redoc-container'));
    } catch (error) {
      document.getElementById('redoc-container').innerHTML =
        '<p>Unable to render API documentation. Please ensure the YAML specification below is valid.</p>';
    }
  }
</script>
<script src="https://cdn.jsdelivr.net/npm/js-yaml@4.1.0/dist/js-yaml.min.js"></script>

### Option 2: Swagger UI Browser

<div id="swagger-ui"></div>
<link rel="stylesheet" type="text/css" href="https://unpkg.com/swagger-ui-dist@5.9.0/swagger-ui.css" />
<script src="https://unpkg.com/swagger-ui-dist@5.9.0/swagger-ui-bundle.js"></script>
<script>
  // Initialize Swagger UI
  window.onload = function() {
    const yamlContent = document.querySelector('pre code.language-yaml')?.textContent ||
                       document.querySelector('code[class*="yaml"]')?.textContent ||
                       document.body.textContent.match(/```yaml\n([\s\S]*?)\n```/)?.[1];

    if (yamlContent) {
      try {
        const spec = jsyaml.load(yamlContent);
        SwaggerUIBundle({
          dom_id: '#swagger-ui',
          spec: spec,
          layout: "StandaloneLayout",
          deepLinking: true,
          showExtensions: true,
          showCommonExtensions: true,
          defaultModelsExpandDepth: 2,
          defaultModelExpandDepth: 2,
          displayOperationId: false,
          tryItOutEnabled: true,
          requestInterceptor: (request) => {
            // Add DCI-required headers
            request.headers['version'] = '1.0.0';
            request.headers['ts'] = new Date().toISOString();
            return request;
          }
        });
      } catch (error) {
        document.getElementById('swagger-ui').innerHTML =
          '<p>Unable to render Swagger UI. Please ensure the YAML specification below is valid.</p>';
      }
    }
  };
</script>

---

## Employment-SP Interface API Specification

```yaml
openapi: 3.0.3
info:
  title: Interoperability APIs - Employment-Social Protection Interface
  x-logo:
    url: ./dci-logo.png
    backgroundColor: '#FFFFFF'
    altText: Digital Convergence Initiative
  version: 1.0.0
  description: |
    The Employment-SP interoperability APIs describe different APIs to perform interoperable operations between Public Employment Services (PES) and Social Protection systems following DCI standards.

    This API extends the core DCI patterns with employment-specific functionality while maintaining full compatibility with existing Social Registry and IBR interfaces.

    1. Referral Management: APIs for creating and tracking employment referrals from SP to PES
    2. Employment Status Verification: Real-time employment status queries for benefit eligibility
    3. Job Placement Tracking: APIs for tracking employment outcomes and placements
    4. Event Subscription: Subscribe/unsubscribe to employment events with async notifications
    5. Compliance Monitoring: APIs for tracking compliance with employment activation requirements

    **DCI Compliance**: All requests follow the DCI three-part structure:
    - signature: Message integrity verification using JWS
    - header: Standard DCI headers with employment-specific extensions
    - message: Encrypted payload using JWE with employment data objects

    Gitbook reference link:
    - [Employment-SP Interface - V1.0](https://standards.spdci.org/standards/employment-sp-v1.0.0)

    Code directory links:
    - [Employment Status](https://standards.spdci.org/standards/employment-sp/data/code-directory/cd.empl.01-employment_status)
    - [Referral Reason](https://standards.spdci.org/standards/employment-sp/data/code-directory/cd.empl.02-referral_reason)
    - [Job Placement Type](https://standards.spdci.org/standards/employment-sp/data/code-directory/cd.empl.03-placement_type)
    - [Compliance Status](https://standards.spdci.org/standards/employment-sp/data/code-directory/cd.empl.04-compliance_status)

    Data Objects:
    - [Employment Referral](https://standards.spdci.org/standards/employment-sp/data/data-objects/do.empl.01-referral)
    - [Job Placement](https://standards.spdci.org/standards/employment-sp/data/data-objects/do.empl.02-placement)
    - [Employment Verification](https://standards.spdci.org/standards/employment-sp/data/data-objects/do.empl.03-verification)

  contact:
    name: DCI Social Protection
    email: info@spdci.org
  license:
    name: DCI Social Protection License
    url: 'https://api.spdci.org/LICENSE.md'

servers:
  - url: 'https://sandbox.spdci.org/employment-sp/v1.0.0'
    description: Sandbox Server
  - url: 'https://api.spdci.org/employment-sp/v1.0.0'
    description: Production Environment
  - url: 'https://api-cl.spdci.org/employment-sp/v1.0.0'
    description: Chile-specific implementation (RSH-SENCE integration)
  - url: 'https://api-kr.spdci.org/employment-sp/v1.0.0'
    description: South Korea implementation (Work24 platform)
  - url: 'https://api-de.spdci.org/employment-sp/v1.0.0'
    description: Germany implementation (SGB framework)
  - url: 'https://api-au.spdci.org/employment-sp/v1.0.0'
    description: Australia implementation (JobActive network)

tags:
  - name: Async
    description: Async endpoints
  - name: Sync
    description: Sync endpoints
  - name: Schemas
    description: Schemas
  - name: Status Codes
    description: Status Codes
  - name: ReferralRequest
    x-displayName: ReferralRequest
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/ReferralRequest" />
  - name: ReferralResponse
    x-displayName: ReferralResponse
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/ReferralResponse" />
  - name: EmploymentVerificationRequest
    x-displayName: EmploymentVerificationRequest
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/EmploymentVerificationRequest" />
  - name: EmploymentVerificationResponse
    x-displayName: EmploymentVerificationResponse
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/EmploymentVerificationResponse" />
  - name: SubscribeRequest
    x-displayName: SubscribeRequest
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/SubscribeRequest" />
  - name: SubscribeResponse
    x-displayName: SubscribeResponse
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/SubscribeResponse" />
  - name: TxnStatusRequest
    x-displayName: TxnStatusRequest
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/TxnStatusRequest" />
  - name: TxnStatusResponse
    x-displayName: TxnStatusResponse
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/TxnStatusResponse" />
  - name: EncryptedMessage
    x-displayName: EncryptedMessage
    description: |
      <SchemaDefinition schemaRef="#/components/schemas/EncryptedMessage" />

x-tagGroups:
  - name: API Definitions
    tags:
      - Async
      - Sync
  - name: Schema Objects
    tags:
      - ReferralRequest
      - ReferralResponse
      - EmploymentVerificationRequest
      - EmploymentVerificationResponse
      - SubscribeRequest
      - SubscribeResponse
      - TxnStatusRequest
      - TxnStatusResponse
      - EncryptedMessage
  - name: Status Codes
    tags:
      - Status Codes

paths:
  /employment/referral:
    post:
      summary: /employment/referral
      description: |
        The async referral API will accept request and will send response to on-referral endpoint.
        The SP-MIS will implement /employment/referral endpoint and the PES shall implement /employment/on-referral to receive referral confirmation from PES.

        The referral request follows DCI three-part structure:
        - signature: JWS signature for message integrity
        - header: DCI headers with employment-specific extensions
        - message: Encrypted JWE payload with employment referral data

        Request fields:
        - person_reference: Reference to person in source system (minimal PII)
        - source_programme: IBR Programme object reference
        - referral_reason: Code from CD.EMPL.02 referral reasons
        - target_pes_jurisdiction: Geographic jurisdiction for PES assignment
        - compliance_requirements: Employment activation obligations
        - consent: Data sharing consent verification
      operationId: createEmploymentReferral
      tags:
        - Async
      security:
        - bearerAuth: []
      parameters:
        - $ref: '#/components/parameters/version'
        - $ref: '#/components/parameters/correlationId'
        - $ref: '#/components/parameters/ts'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReferralRequest'
            examples:
              employment_referral:
                summary: Employment referral from SP to PES
                value:
                  signature: "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
                  header:
                    message_id: "550e8400-e29b-41d4-a716-446655440000"
                    message_ts: "2025-09-15T14:30:00.000Z"
                    action: "referral"
                    sender_id: "SP-MIS-CL"
                    receiver_id: "PES-CL"
                    sender_uri: "https://api-cl.sp-mis.gov/v1"
                    total_count: 1
                  message:
                    transaction_id: "550e8400-e29b-41d4-a716-446655440001"
                    referral_data:
                      person_reference: "SP_REF_12345"
                      source_programme:
                        "@type": "spdci:Programme"
                        programme_identifier: "IBR-CASH-2024"
                        programme_name: "Ingreso Familiar de Emergencia"
                      referral_reason: "unemployment_activation"
                      target_pes_jurisdiction: "REGION_METROPOLITANA"
                      compliance_requirements:
                        job_search_obligation: true
                        reporting_frequency: "monthly"
                        training_participation: false
                      consent:
                        consent_given: true
                        consent_timestamp: "2025-09-15T14:00:00.000Z"
                        data_retention_period: "P2Y"
                        consent_date: "2025-09-14T10:00:00.000Z"
                        consent_type: "employment_services"
                        legal_basis: "social_protection_law_2023"
                      locale: "es-CL"
      responses:
        '201':
          description: Referral(s) successfully created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ReferralResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/UnprocessableEntity'
        '429':
          $ref: '#/components/responses/TooManyRequests'
        '500':
          $ref: '#/components/responses/InternalServerError'

  /employment/on-referral:
    post:
      summary: /employment/on-referral
      description: |
        Callback endpoint for referral results. PES systems implement this endpoint to receive
        referral confirmations from SP-MIS following DCI async callback pattern.
      operationId: onEmploymentReferral
      tags:
        - Async
      security:
        - bearerAuth: []
      parameters:
        - $ref: '#/components/parameters/version'
        - $ref: '#/components/parameters/correlationId'
        - $ref: '#/components/parameters/ts'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReferralResponse'
      responses:
        '200':
          $ref: '#/components/responses/StandardResponse'
        '401':
          $ref: '#/components/responses/HttpErrorResponse'
        '403':
          $ref: '#/components/responses/HttpErrorResponse'
        '500':
          $ref: '#/components/responses/HttpErrorResponse'

    get:
      summary: Search Employment Referrals
      description: |
        Search and retrieve employment referrals by various criteria.
        Supports pagination and filtering for large datasets.
      operationId: searchReferrals
      tags:
        - Referrals
      security:
        - OAuth2ClientCredentials:
            - referral:read
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
        - name: person_id
          in: query
          description: Filter by person identifier
          schema:
            type: string
        - name: status
          in: query
          description: Filter by referral status
          schema:
            type: string
            enum: [pending, accepted, rejected, completed]
        - name: source_programme
          in: query
          description: Filter by source SP programme
          schema:
            type: string
        - name: created_from
          in: query
          description: Filter referrals created from this date (ISO 8601)
          schema:
            type: string
            format: date-time
        - name: created_to
          in: query
          description: Filter referrals created until this date (ISO 8601)
          schema:
            type: string
            format: date-time
        - $ref: '#/components/parameters/Limit'
        - $ref: '#/components/parameters/Offset'
      responses:
        '200':
          description: Referrals retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ReferralSearchResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

  /referrals/{referral_id}:
    get:
      summary: Get Employment Referral
      description: Retrieve a specific employment referral by ID
      operationId: getReferral
      tags:
        - Referrals
      security:
        - OAuth2ClientCredentials:
            - referral:read
      parameters:
        - name: referral_id
          in: path
          required: true
          description: Unique referral identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Referral retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentReferral'
        '404':
          $ref: '#/components/responses/NotFound'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

    put:
      summary: Update Employment Referral
      description: Update the status and details of an employment referral
      operationId: updateReferral
      tags:
        - Referrals
      security:
        - OAuth2ClientCredentials:
            - referral:update
      parameters:
        - name: referral_id
          in: path
          required: true
          description: Unique referral identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReferralUpdate'
      responses:
        '200':
          description: Referral updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentReferral'
        '400':
          $ref: '#/components/responses/BadRequest'
        '404':
          $ref: '#/components/responses/NotFound'
        '409':
          $ref: '#/components/responses/Conflict'

  /employment-status/{person_id}:
    get:
      summary: Get Employment Status
      description: |
        Retrieve employment status verification for SP benefit compliance.
        Supports historical data and multiple verification sources.
      operationId: getEmploymentStatus
      tags:
        - Employment Status
      security:
        - OAuth2ClientCredentials:
            - employment:read
      parameters:
        - name: person_id
          in: path
          required: true
          description: Unique person identifier
          schema:
            type: string
        - name: verification_date
          in: query
          description: ISO 8601 date for historical status
          schema:
            type: string
            format: date
        - name: include_history
          in: query
          description: Include employment history
          schema:
            type: boolean
            default: false
        - name: verification_sources
          in: query
          description: Comma-separated list of sources to check
          schema:
            type: string
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
        - $ref: '#/components/parameters/AcceptLanguage'
      responses:
        '200':
          description: Employment status retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentStatusResponse'
        '404':
          $ref: '#/components/responses/NotFound'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

  /employment-benefits:
    post:
      summary: Create Employment Benefits
      description: |
        Create or enroll beneficiaries in employment-related benefits
        (training, work opportunities) coordinated between SP and PES systems.
      operationId: createEmploymentBenefits
      tags:
        - Employment Benefits
      security:
        - OAuth2ClientCredentials:
            - benefit:manage
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EmploymentBenefitRequest'
      responses:
        '201':
          description: Employment benefit(s) created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentBenefitResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '422':
          $ref: '#/components/responses/UnprocessableEntity'

    get:
      summary: Search Employment Benefits
      description: Search employment benefits by person, type, or status
      operationId: searchEmploymentBenefits
      tags:
        - Employment Benefits
      security:
        - OAuth2ClientCredentials:
            - benefit:read
      parameters:
        - name: person_id
          in: query
          description: Filter by person identifier
          schema:
            type: string
        - name: benefit_type
          in: query
          description: Filter by benefit type
          schema:
            type: string
            enum: [training, work_opportunity, employment_subsidy]
        - name: status
          in: query
          description: Filter by benefit status
          schema:
            type: string
            enum: [enrolled, active, completed, withdrawn, suspended]
        - $ref: '#/components/parameters/Limit'
        - $ref: '#/components/parameters/Offset'
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Employment benefits retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentBenefitSearchResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /employment-benefits/{benefit_id}:
    get:
      summary: Get Employment Benefit
      description: Retrieve specific employment benefit details
      operationId: getEmploymentBenefit
      tags:
        - Employment Benefits
      security:
        - OAuth2ClientCredentials:
            - benefit:read
      parameters:
        - name: benefit_id
          in: path
          required: true
          description: Unique benefit identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Employment benefit retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentBenefit'
        '404':
          $ref: '#/components/responses/NotFound'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

    put:
      summary: Update Employment Benefit
      description: Update employment benefit status, progress, or outcomes
      operationId: updateEmploymentBenefit
      tags:
        - Employment Benefits
      security:
        - OAuth2ClientCredentials:
            - benefit:manage
      parameters:
        - name: benefit_id
          in: path
          required: true
          description: Unique benefit identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EmploymentBenefitUpdate'
      responses:
        '200':
          description: Employment benefit updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/EmploymentBenefit'
        '400':
          $ref: '#/components/responses/BadRequest'
        '404':
          $ref: '#/components/responses/NotFound'
        '409':
          $ref: '#/components/responses/Conflict'

  /job-placements:
    post:
      summary: Create Job Placements
      description: Register job placement events from PES to SP systems
      operationId: createJobPlacements
      tags:
        - Job Placements
      security:
        - OAuth2ClientCredentials:
            - placement:create
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/JobPlacementRequest'
      responses:
        '201':
          description: Job placement(s) created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/JobPlacementResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

    get:
      summary: Search Job Placements
      description: Search job placements by person, employer, or date range
      operationId: searchJobPlacements
      tags:
        - Job Placements
      security:
        - OAuth2ClientCredentials:
            - placement:read
      parameters:
        - name: person_id
          in: query
          description: Filter by person identifier
          schema:
            type: string
        - name: employer_name
          in: query
          description: Filter by employer name
          schema:
            type: string
        - name: placement_from
          in: query
          description: Filter placements from this date
          schema:
            type: string
            format: date
        - name: placement_to
          in: query
          description: Filter placements until this date
          schema:
            type: string
            format: date
        - $ref: '#/components/parameters/Limit'
        - $ref: '#/components/parameters/Offset'
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Job placements retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/JobPlacementSearchResponse'

  /job-placements/{placement_id}:
    get:
      summary: Get Job Placement
      description: Retrieve specific job placement details
      operationId: getJobPlacement
      tags:
        - Job Placements
      security:
        - OAuth2ClientCredentials:
            - placement:read
      parameters:
        - name: placement_id
          in: path
          required: true
          description: Unique placement identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Job placement retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/JobPlacement'
        '404':
          $ref: '#/components/responses/NotFound'

    put:
      summary: Update Job Placement
      description: Update job placement status and outcomes for SP benefit management
      operationId: updateJobPlacement
      tags:
        - Job Placements
      security:
        - OAuth2ClientCredentials:
            - placement:update
      parameters:
        - name: placement_id
          in: path
          required: true
          description: Unique placement identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/JobPlacementUpdate'
      responses:
        '200':
          description: Job placement updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/JobPlacementUpdateResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '404':
          $ref: '#/components/responses/NotFound'

  /compliance-monitoring:
    get:
      summary: Get Compliance Monitoring
      description: |
        Retrieve compliance status for employment obligations of SP beneficiaries.
        Supports filtering by person, period, and obligation types.
      operationId: getComplianceMonitoring
      tags:
        - Compliance Monitoring
      security:
        - OAuth2ClientCredentials:
            - compliance:read
      parameters:
        - name: person_id
          in: query
          required: true
          description: Person identifier
          schema:
            type: string
        - name: monitoring_period_start
          in: query
          description: Start date for monitoring period
          schema:
            type: string
            format: date
        - name: monitoring_period_end
          in: query
          description: End date for monitoring period
          schema:
            type: string
            format: date
        - name: obligation_types
          in: query
          description: Comma-separated list of obligation types
          schema:
            type: string
        - name: compliance_status
          in: query
          description: Filter by compliance status
          schema:
            type: string
            enum: [compliant, partial_compliance, non_compliant]
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Compliance monitoring data retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ComplianceMonitoring'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

  /notifications:
    post:
      summary: Send Cross-System Notifications
      description: |
        Send notifications between PES and SP systems for status changes,
        compliance alerts, and coordination events.
      operationId: sendNotifications
      tags:
        - Notifications
      security:
        - OAuth2ClientCredentials:
            - notification:send
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NotificationRequest'
      responses:
        '200':
          description: Notification(s) sent successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

  /webhooks/subscription:
    post:
      summary: Create Webhook Subscription
      description: |
        Create webhook subscriptions for real-time notifications
        of employment-related events between systems.
      operationId: createWebhookSubscription
      tags:
        - Webhooks
      security:
        - OAuth2ClientCredentials:
            - webhook:manage
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookSubscriptionRequest'
      responses:
        '201':
          description: Webhook subscription created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WebhookSubscription'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'

    get:
      summary: List Webhook Subscriptions
      description: Retrieve active webhook subscriptions for the authenticated client
      operationId: listWebhookSubscriptions
      tags:
        - Webhooks
      security:
        - OAuth2ClientCredentials:
            - webhook:read
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
        - name: event_type
          in: query
          description: Filter by event type
          schema:
            type: string
        - name: status
          in: query
          description: Filter by subscription status
          schema:
            type: string
            enum: [active, paused, disabled]
      responses:
        '200':
          description: Webhook subscriptions retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WebhookSubscriptionList'

  /webhooks/subscription/{subscription_id}:
    get:
      summary: Get Webhook Subscription
      description: Retrieve specific webhook subscription details
      operationId: getWebhookSubscription
      tags:
        - Webhooks
      security:
        - OAuth2ClientCredentials:
            - webhook:read
      parameters:
        - name: subscription_id
          in: path
          required: true
          description: Unique subscription identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Webhook subscription retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WebhookSubscription'
        '404':
          $ref: '#/components/responses/NotFound'

  /job-matching/ai-enhanced:
    post:
      summary: AI-Enhanced Job Matching
      description: |
        Advanced job matching using machine learning algorithms based on
        South Korea's WorkNet AI system. Provides skills-based matching,
        career pathway optimization, and employment sustainability predictions.
      operationId: aiEnhancedJobMatching
      tags:
        - AI Services
      security:
        - OAuth2ClientCredentials:
            - employment:read
            - ai:job_matching
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
        - $ref: '#/components/parameters/CountryImplementation'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AIJobMatchingRequest'
      responses:
        '200':
          description: AI job matching results generated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AIJobMatchingResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

  /mobile/job-diary:
    get:
      summary: Mobile Job Diary (모바일 취업일지)
      description: |
        Mobile compliance tracking system inspired by South Korea's Work24
        mobile Job Diary. Provides real-time job search activity tracking
        and compliance monitoring for beneficiaries.
      operationId: getMobileJobDiary
      tags:
        - Mobile Services
      security:
        - OAuth2ClientCredentials:
            - compliance:read
            - mobile:access
      parameters:
        - name: person_id
          in: query
          required: true
          description: Person identifier for job diary access
          schema:
            type: string
        - name: period_start
          in: query
          description: Start date for diary period
          schema:
            type: string
            format: date
        - name: period_end
          in: query
          description: End date for diary period
          schema:
            type: string
            format: date
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Mobile job diary retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/MobileJobDiary'
        '404':
          $ref: '#/components/responses/NotFound'

    post:
      summary: Update Mobile Job Diary
      description: Add job search activities and compliance updates via mobile interface
      operationId: updateMobileJobDiary
      tags:
        - Mobile Services
      security:
        - OAuth2ClientCredentials:
            - compliance:update
            - mobile:access
      parameters:
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/MobileJobDiaryUpdate'
      responses:
        '200':
          description: Job diary updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/MobileJobDiary'
        '400':
          $ref: '#/components/responses/BadRequest'

  /countries/{country_code}/performance-benchmarks:
    get:
      summary: Country Performance Benchmarks
      description: |
        Retrieve real operational performance benchmarks from country implementations
        including processing times, success rates, and integration metrics.
      operationId: getCountryPerformanceBenchmarks
      tags:
        - Country Analytics
      security:
        - OAuth2ClientCredentials:
            - analytics:read
      parameters:
        - name: country_code
          in: path
          required: true
          description: ISO 3166-1 alpha-2 country code
          schema:
            type: string
            pattern: '^[A-Z]{2}$'
            example: "CL"
        - name: metric_type
          in: query
          description: Filter by specific metric type
          schema:
            type: string
            enum: [processing_time, success_rate, integration_performance, user_satisfaction]
        - name: time_period
          in: query
          description: Time period for metrics (default: last 12 months)
          schema:
            type: string
            enum: [month, quarter, year]
            default: year
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '200':
          description: Country performance benchmarks retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CountryPerformanceBenchmarks'
        '404':
          $ref: '#/components/responses/NotFound'

    put:
      summary: Update Webhook Subscription
      description: Update webhook subscription configuration or status
      operationId: updateWebhookSubscription
      tags:
        - Webhooks
      security:
        - OAuth2ClientCredentials:
            - webhook:manage
      parameters:
        - name: subscription_id
          in: path
          required: true
          description: Unique subscription identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookSubscriptionUpdate'
      responses:
        '200':
          description: Webhook subscription updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WebhookSubscription'
        '400':
          $ref: '#/components/responses/BadRequest'
        '404':
          $ref: '#/components/responses/NotFound'

    delete:
      summary: Delete Webhook Subscription
      description: Delete a webhook subscription
      operationId: deleteWebhookSubscription
      tags:
        - Webhooks
      security:
        - OAuth2ClientCredentials:
            - webhook:manage
      parameters:
        - name: subscription_id
          in: path
          required: true
          description: Unique subscription identifier
          schema:
            type: string
            format: uuid
        - $ref: '#/components/parameters/CorrelationId'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/ApiVersion'
      responses:
        '204':
          description: Webhook subscription deleted successfully
        '404':
          $ref: '#/components/responses/NotFound'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: jwt
      description: "User/System authenticated access token; (jwt bearer) token returned from implementing system's authentication/token api end points. All systems must implement token api."

  parameters:
    version:
      in: header
      name: version
      required: true
      description: API version
      schema:
        type: string
        example: "1.0.0"

    correlationId:
      in: header
      name: X-Correlation-ID
      required: true
      description: Correlation ID for tracking requests across systems
      schema:
        type: string
        format: uuid
        example: "550e8400-e29b-41d4-a716-446655440000"

    ts:
      in: header
      name: ts
      required: true
      description: Timestamp of the request
      schema:
        type: string
        format: date-time
        example: "2025-09-15T14:30:00.000Z"
        example: "550e8400-e29b-41d4-a716-446655440001"

    ApiVersion:
      name: X-API-Version
      in: header
      required: true
      description: API version for request processing
      schema:
        type: string
        pattern: '^\d+\.\d+\.\d+$'
        example: "1.0.0"

    AcceptLanguage:
      name: Accept-Language
      in: header
      description: Language preference for response messages
      schema:
        type: string
        example: "es-CL,en-US;q=0.8"

    CountryImplementation:
      name: X-Country-Implementation
      in: header
      description: Country-specific implementation pattern
      schema:
        type: string
        enum: [CL-RSH-SENCE, KR-WORK24, DE-SGB, AU-JOBACTIVE, TR-ISKUR-ISAF]
        example: "CL-RSH-SENCE"

    IntegrationPattern:
      name: X-Integration-Pattern
      in: header
      description: Technical integration pattern being used
      schema:
        type: string
        enum: [real_time_api_coordination, integrated_service_delivery, market_based_service, staged_system_integration]
        example: "real_time_api_coordination"

    LegalFramework:
      name: X-Legal-Framework
      in: header
      description: Legal framework basis for data processing
      schema:
        type: string
        example: "laws_19628_21719"

    PerformanceBenchmark:
      name: X-Performance-Benchmark
      in: header
      description: Expected performance benchmark for this implementation
      schema:
        type: string
        example: "chile_18h_processing_98_7pct_consistency"

    Limit:
      name: limit
      in: query
      description: Maximum number of items to return (default 50, max 500)
      schema:
        type: integer
        minimum: 1
        maximum: 500
        default: 50

    Offset:
      name: offset
      in: query
      description: Number of items to skip for pagination
      schema:
        type: integer
        minimum: 0
        default: 0

  schemas:
    # DCI Core Message Structure
    ReferralRequest:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
          description: "JWS signature for message integrity verification"
          example: "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
        header:
          $ref: '#/components/schemas/MessageHeader'
        message:
          $ref: '#/components/schemas/EncryptedMessage'

    MessageHeader:
      type: object
      description: "Message header following DCI MsgHeader_V1.0.0 specification"
      required:
        - message_id
        - message_ts
        - action
        - sender_id
        - total_count
      properties:
        version:
          type: string
          default: "1.0.0"
          description: "Messaging protocol specification version being used"
        message_id:
          type: string
          description: "Unique message id to communicate between sender and receiver systems"
          example: "123"
        message_ts:
          type: string
          format: date-time
          description: "Message timestamp"
        action:
          type: string
          description: "SPDCI Connect specific action. Usually verb from the URI"
        sender_id:
          type: string
          description: "sender_id registered with the receiving system or gateway"
          example: "spp.example.org"
        sender_uri:
          type: string
          format: uri
          description: "sender url to accept callbacks. Applicable only for async communications"
          example: "https://spp.example.org/{namespace}/callback/on-search"
        receiver_id:
          type: string
          description: "receiver id registered with the calling system"
          example: "civilregistry.example.org"
        total_count:
          type: integer
          description: "Total no of requests present in the message request"
          example: 21800
        is_msg_encrypted:
          type: boolean
          default: false
          description: "Is message encrypted?"
        meta:
          type: object
          description: "Additional metadata"

    EncryptedMessage:
      type: object
      description: "Encrypted message payload using JWE"
      properties:
        encrypted:
          type: string
          description: "JWE encrypted message content"
        alg:
          type: string
          description: "Encryption algorithm used"
        enc:
          type: string
          description: "Content encryption algorithm"

    # Employment-SP Specific Schemas (extending DCI IBR schemas)
    EmploymentReferralData:
      type: object
      required:
        - transaction_id
        - referral_data
      properties:
        transaction_id:
          type: string
          format: uuid
          description: "Transaction identifier for this referral"
        referral_data:
          $ref: '#/components/schemas/ReferralData'

    ReferralData:
      type: object
      required:
        - person_reference
        - source_programme
        - referral_reason
        - consent
      properties:
        person_reference:
          type: string
          description: "Reference to person in source system (minimal PII)"
        source_programme:
          $ref: '#/components/schemas/ProgrammeReference'
        referral_reason:
          type: string
          enum: ["unemployment_activation", "training_referral", "job_placement_support", "compliance_monitoring", "benefit_transition"]
          description: "Reason for employment referral (from CD.EMPL.02)"
        target_pes_jurisdiction:
          type: string
          description: "Geographic jurisdiction for PES assignment"
        compliance_requirements:
          $ref: '#/components/schemas/ComplianceRequirements'
        consent:
          $ref: '#/components/schemas/ConsentData'

    ProgrammeReference:
      type: object
      description: "Reference to IBR Programme object"
      required:
        - "@type"
        - programme_identifier
      properties:
        "@type":
          type: string
          const: "spdci:Programme"
        programme_identifier:
          type: string
          description: "IBR programme identifier"
        programme_name:
          type: string
          description: "Programme name for reference"

    ComplianceRequirements:
      type: object
      properties:
        job_search_obligation:
          type: boolean
          description: "Whether beneficiary has job search obligation"
        reporting_frequency:
          type: string
          enum: ["weekly", "bi_weekly", "monthly", "quarterly"]
          description: "Required reporting frequency"
        training_participation:
          type: boolean
          description: "Whether training participation is required"
        geographic_mobility:
          type: boolean
          description: "Whether geographic mobility is required"
        work_capacity_restrictions:
          type: array
          items:
            type: string
          description: "Any work capacity restrictions"

    ConsentData:
      type: object
      required:
        - consent_given
        - consent_timestamp
      properties:
        consent_given:
          type: boolean
          description: "Whether data sharing consent is given"
        consent_timestamp:
          type: string
          format: date-time
          description: "When consent was given"
        data_retention_period:
          type: string
          format: duration
          description: "ISO 8601 duration for data retention"
        consent_type:
          type: string
          enum: ["employment_services", "training_services", "job_placement", "compliance_monitoring"]
          description: "Type of consent given"
        legal_basis:
          type: string
          description: "Legal basis for data processing"

    # DCI Standard Response Schemas
    ReferralResponse:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
          description: "JWS signature for response integrity"
        header:
          $ref: '#/components/schemas/ResponseHeader'
        message:
          $ref: '#/components/schemas/ReferralResponseMessage'

    ResponseHeader:
      type: object
      description: "Message callback header following DCI MsgCallbackHeader_V1.0.0 specification"
      required:
        - message_id
        - message_ts
        - action
        - status
      properties:
        version:
          type: string
          default: "1.0.0"
          description: "Messaging protocol specification version being used"
        message_id:
          type: string
          description: "Unique message id to communicate between sender and receiver systems"
          example: "789"
        message_ts:
          type: string
          format: date-time
          description: "Message timestamp"
        action:
          type: string
          description: "SPDCI Connect specific action"
        status:
          $ref: '#/components/schemas/RequestStatus'
        status_reason_code:
          type: string
          description: "Status reason code for detailed error information"
        status_reason_message:
          type: string
          maxLength: 999
          description: "Status reason code message, if any. Helps actionable messaging for system/end users"
        total_count:
          type: integer
          description: "Total no of requests present in the message request"
          example: 21800
        completed_count:
          type: integer
          description: "No of requests in completed state. Complete includes success and error requests due to functional errors"
          example: 50
        sender_id:
          type: string
          description: "sender_id registered with the receiving system or gateway"
          example: "civilregistry.example.org"
        receiver_id:
          type: string
          description: "receiver id registered with the calling system"
          example: "registry.example.org"
        is_msg_encrypted:
          type: boolean
          default: false
          description: "Is message encrypted?"
        meta:
          type: object
          description: "Additional metadata"

    ReferralResponseMessage:
      type: object
      properties:
        transaction_id:
          type: string
          format: uuid
          description: "Transaction identifier from request"
        referral_id:
          type: string
          format: uuid
          description: "Generated referral identifier"
        status:
          type: string
          enum: ["accepted", "rejected", "pending_review"]
          description: "Referral processing status"
        pes_case_number:
          type: string
          description: "PES case number assigned"
        assigned_caseworker:
          type: string
          description: "Assigned PES caseworker identifier"
        next_contact_date:
          type: string
          format: date
          description: "Scheduled next contact date"
        rejection_reasons:
          type: array
          items:
            type: string
          description: "Reasons for rejection if applicable"

    # DCI Standard Components
    RequestStatus:
      type: string
      description: "Request status following DCI patterns"
      enum:
        - "rcvd"  # Received
        - "pdng"  # Pending
        - "succ"  # Success
        - "rjct"  # Rejected

    # DCI Subscribe/Unsubscribe Support
    SubscribeRequest:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
          description: "JWS signature for message integrity"
        header:
          $ref: '#/components/schemas/MessageHeader'
        message:
          $ref: '#/components/schemas/SubscriptionMessage'

    SubscriptionMessage:
      type: object
      required:
        - event_types
        - callback_url
      properties:
        event_types:
          type: array
          items:
            type: string
            enum: ["referral_created", "referral_updated", "employment_status_changed", "placement_created", "compliance_alert"]
          description: "Types of events to subscribe to"
        callback_url:
          type: string
          format: uri
          description: "URL to receive event notifications"
        filter_criteria:
          type: object
          description: "Optional filters for events"
        subscription_duration:
          type: string
          format: duration
          description: "ISO 8601 duration for subscription"

    SubscribeResponse:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
        header:
          $ref: '#/components/schemas/ResponseHeader'
        message:
          $ref: '#/components/schemas/SubscriptionResponseMessage'

    SubscriptionResponseMessage:
      type: object
      properties:
        subscription_id:
          type: string
          format: uuid
          description: "Generated subscription identifier"
        status:
          type: string
          enum: ["active", "pending", "rejected"]
        expiry_date:
          type: string
          format: date-time
          description: "Subscription expiry date"

    # DCI Transaction Status Support
    TxnStatusRequest:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
        header:
          $ref: '#/components/schemas/MessageHeader'
        message:
          $ref: '#/components/schemas/StatusQueryMessage'

    StatusQueryMessage:
      type: object
      required:
        - transaction_id
      properties:
        transaction_id:
          type: string
          format: uuid
          description: "Transaction ID to query status for"

    TxnStatusResponse:
      type: object
      required:
        - signature
        - header
        - message
      properties:
        signature:
          type: string
        header:
          $ref: '#/components/schemas/ResponseHeader'
        message:
          $ref: '#/components/schemas/StatusResponseMessage'

    StatusResponseMessage:
      type: object
      properties:
        transaction_id:
          type: string
          format: uuid
        current_status:
          $ref: '#/components/schemas/RequestStatus'
        status_details:
          type: string
          description: "Detailed status information"
        last_updated:
          type: string
          format: date-time
          enum: [unemployment_benefit_activation, work_capacity_assessment, compliance_requirement, training_opportunity]
        work_capacity_assessment:
          $ref: '#/components/schemas/WorkCapacityAssessment'
        referral_date:
          type: string
          format: date-time
          description: ISO 8601 UTC timestamp
        target_pes_office:
          type: string
          description: Target PES office identifier
        status:
          type: string
          enum: [pending, accepted, rejected, completed]
        compliance_requirements:
          $ref: '#/components/schemas/ComplianceRequirements'
        created_by:
          type: string
          description: System or user that created the referral
        created_at:
          type: string
          format: date-time

    Person:
      type: object
      required:
        - identifiers
      properties:
        '@type':
          type: string
          enum: ['Person']
        '@id':
          type: string
          description: Person entity reference
        identifiers:
          type: array
          items:
            $ref: '#/components/schemas/Identifier'
          minItems: 1
        name:
          $ref: '#/components/schemas/Name'
        contact_info:
          $ref: '#/components/schemas/ContactInfo'

    Identifier:
      type: object
      required:
        - identifier_type
        - identifier_value
      properties:
        '@type':
          type: string
          enum: ['Identifier']
        identifier_type:
          type: string
          enum: [national_id, passport, social_security, tax_id, system_id]
        identifier_value:
          type: string
          description: Identifier value (encrypted or hashed for PII protection)
        verification_status:
          type: string
          enum: [verified, unverified, pending_verification]

    Name:
      type: object
      required:
        - given_name
        - surname
      properties:
        '@type':
          type: string
          enum: ['Name']
        given_name:
          type: string
          description: First/given name
        surname:
          type: string
          description: Last/family name
        middle_name:
          type: string
          description: Middle name or names

    ContactInfo:
      type: object
      properties:
        phone:
          type: string
          description: Primary phone number
        email:
          type: string
          format: email
          description: Primary email address
        address:
          $ref: '#/components/schemas/Address'

    Address:
      type: object
      properties:
        '@type':
          type: string
          enum: ['Address']
        street_address:
          type: string
        locality:
          type: string
          description: City or locality
        region:
          type: string
          description: State, province, or region
        postal_code:
          type: string
        country_code:
          type: string
          pattern: '^[A-Z]{2}$'
          description: ISO 3166-1 alpha-2 country code

    Programme:
      type: object
      required:
        - programme_identifier
        - programme_name
      properties:
        '@type':
          type: string
          enum: ['ibr:Programme']
        programme_identifier:
          type: string
          description: Unique programme identifier
        programme_name:
          type: string
          description: Human-readable programme name
        implementing_institution:
          type: string
          description: Institution implementing the programme

    WorkCapacityAssessment:
      type: object
      properties:
        assessment_date:
          type: string
          format: date
        work_able:
          type: boolean
          description: Assessment result - able to work
        restrictions:
          type: array
          items:
            type: string
            enum: [part_time_only, light_duties, no_physical_labor, accommodations_required]
        skills:
          type: array
          items:
            type: string
          description: Identified skills and competencies
        work_preferences:
          $ref: '#/components/schemas/WorkPreferences'

    WorkPreferences:
      type: object
      properties:
        preferred_sectors:
          type: array
          items:
            type: string
          description: Preferred employment sectors
        geographic_mobility:
          type: string
          enum: [local_only, regional, national, international]
        availability:
          type: string
          enum: [immediate, within_week, within_month, flexible]

    ComplianceRequirements:
      type: object
      properties:
        job_search_obligation:
          type: boolean
          description: Required to actively search for employment
        training_participation:
          type: string
          enum: [required, recommended, optional]
        reporting_frequency:
          type: string
          enum: [weekly, bi_weekly, monthly, quarterly]

    Currency:
      type: object
      required:
        - amount
        - currency_code
      properties:
        '@type':
          type: string
          enum: ['Currency']
        amount:
          type: string
          pattern: '^\d+(\.\d{1,4})?$'
          description: Monetary amount as string to preserve precision
        currency_code:
          type: string
          pattern: '^[A-Z]{3}$'
          description: ISO 4217 currency code

    EmploymentStatusResponse:
      type: object
      required:
        - transaction_id
        - person_id
        - employment_status
      properties:
        transaction_id:
          type: string
          format: uuid
        correlation_id:
          type: string
          format: uuid
        person_id:
          type: string
        employment_status:
          $ref: '#/components/schemas/EmploymentStatus'
        employment_history:
          type: array
          items:
            $ref: '#/components/schemas/EmploymentHistoryItem'
        sp_benefit_impact:
          $ref: '#/components/schemas/SPBenefitImpact'

    EmploymentStatus:
      type: object
      required:
        - current_status
        - status_date
        - verification_date
      properties:
        current_status:
          type: string
          enum: [employed, unemployed, seeking_work, not_in_labor_force, student, retired]
        status_date:
          type: string
          format: date
          description: Date when current status became effective
        verification_date:
          type: string
          format: date
          description: Date when status was last verified
        confidence_level:
          type: string
          enum: [low, medium, high]
        verification_sources:
          type: array
          items:
            type: string
            enum: [payroll_system, tax_authority, employer_declaration, social_security]
        employment_details:
          $ref: '#/components/schemas/EmploymentDetails'

    EmploymentDetails:
      type: object
      properties:
        employer_name:
          type: string
        employer_tax_id:
          type: string
        job_title:
          type: string
        employment_type:
          type: string
          enum: [permanent_full_time, permanent_part_time, temporary_full_time, temporary_part_time, contract, freelance]
        monthly_income:
          $ref: '#/components/schemas/Currency'
        start_date:
          type: string
          format: date
        end_date:
          type: string
          format: date
          nullable: true

    EmploymentHistoryItem:
      type: object
      properties:
        employment_period:
          $ref: '#/components/schemas/DateRange'
        employer_name:
          type: string
        employment_type:
          type: string
          enum: [permanent_full_time, permanent_part_time, temporary_full_time, temporary_part_time, contract, freelance]
        termination_reason:
          type: string
          enum: [resignation, termination, contract_completion, layoff, retirement]

    DateRange:
      type: object
      required:
        - start_date
      properties:
        start_date:
          type: string
          format: date
        end_date:
          type: string
          format: date
          nullable: true

    SPBenefitImpact:
      type: object
      properties:
        benefit_continuation_eligible:
          type: boolean
          description: Whether beneficiary remains eligible for SP benefits
        graduation_triggered:
          type: boolean
          description: Whether employment triggers benefit graduation
        income_supplement_needed:
          type: boolean
          description: Whether income supplementation is required
        next_review_date:
          type: string
          format: date

    EmploymentBenefit:
      type: object
      required:
        - benefit_id
        - person_id
        - benefit_type
        - start_date
      properties:
        '@context':
          type: object
        '@type':
          type: string
          enum: ['empl:EmploymentBenefit']
        benefit_id:
          type: string
          format: uuid
        person_id:
          type: string
        benefit_type:
          type: string
          enum: [training, work_opportunity, employment_subsidy]
        employment_benefit_subtype:
          type: string
          enum: [vocational_skills_training, academic_education, work_experience, apprenticeship, wage_subsidy, hiring_incentive]
        benefit_description:
          type: string
        training_details:
          $ref: '#/components/schemas/TrainingDetails'
        benefit_value:
          $ref: '#/components/schemas/Currency'
        stipend:
          $ref: '#/components/schemas/Stipend'
        start_date:
          type: string
          format: date
        end_date:
          type: string
          format: date
        attendance_requirement:
          type: number
          minimum: 0.0
          maximum: 1.0
          description: Required attendance rate (0.0 - 1.0)
        performance_requirement:
          type: string
        employment_placement_support:
          $ref: '#/components/schemas/EmploymentPlacementSupport'
        compliance_tracking:
          $ref: '#/components/schemas/ComplianceTracking'

    TrainingDetails:
      type: object
      properties:
        training_provider:
          type: string
        course_name:
          type: string
        duration_weeks:
          type: integer
          minimum: 1
        schedule:
          type: string
          enum: [full_time, part_time_morning, part_time_afternoon, part_time_evening, weekend]
        certification_type:
          type: string
          enum: [none, completion_certificate, industry_recognized, accredited_qualification]
        expected_outcome:
          type: string
          enum: [skill_development, employment_placement, career_advancement, entrepreneurship]

    Stipend:
      allOf:
        - $ref: '#/components/schemas/Currency'
        - type: object
          properties:
            frequency:
              type: string
              enum: [weekly, bi_weekly, monthly]

    EmploymentPlacementSupport:
      type: object
      properties:
        job_matching_included:
          type: boolean
        placement_target_days:
          type: integer
          description: Target days for job placement after training completion
        follow_up_period_months:
          type: integer
          description: Post-placement follow-up period in months

    ComplianceTracking:
      type: object
      properties:
        attendance_rate:
          type: number
          minimum: 0.0
          maximum: 1.0
          nullable: true
        performance_score:
          type: number
          nullable: true
        completion_status:
          type: string
          enum: [enrolled, active, completed, withdrawn, suspended]

    JobPlacement:
      type: object
      required:
        - placement_id
        - person_id
        - employer
        - job_details
        - placement_date
      properties:
        '@context':
          type: object
        '@type':
          type: string
          enum: ['empl:JobPlacement']
        placement_id:
          type: string
          format: uuid
        person_id:
          type: string
        referral_id:
          type: string
          format: uuid
          nullable: true
        employer:
          $ref: '#/components/schemas/Employer'
        job_details:
          $ref: '#/components/schemas/JobDetails'
        compensation:
          $ref: '#/components/schemas/Compensation'
        placement_date:
          type: string
          format: date
        start_date:
          type: string
          format: date
        placement_method:
          type: string
          enum: [pes_job_matching, direct_application, referral, recruitment_agency, training_placement]
        pes_support_provided:
          type: array
          items:
            type: string
            enum: [cv_preparation, interview_coaching, skill_assessment, employer_negotiation, workplace_mediation]
        follow_up_schedule:
          $ref: '#/components/schemas/FollowUpSchedule'
        sp_benefit_impact:
          $ref: '#/components/schemas/SPBenefitImpact'

    Employer:
      type: object
      required:
        - employer_name
      properties:
        employer_name:
          type: string
        employer_tax_id:
          type: string
        sector:
          type: string
        company_size:
          type: string
          enum: [micro, small, medium, large]

    JobDetails:
      type: object
      required:
        - job_title
        - employment_type
      properties:
        job_title:
          type: string
        job_category:
          type: string
        employment_type:
          type: string
          enum: [permanent_full_time, permanent_part_time, temporary_full_time, temporary_part_time, contract, freelance]
        contract_duration:
          type: integer
          nullable: true
          description: Contract duration in months (null for permanent)
        probation_period_days:
          type: integer
        work_location:
          type: string
        remote_work_option:
          type: boolean

    Compensation:
      type: object
      properties:
        base_salary:
          $ref: '#/components/schemas/Currency'
        variable_compensation:
          allOf:
            - $ref: '#/components/schemas/Currency'
            - type: object
              properties:
                type:
                  type: string
                  enum: [commission, bonus, overtime, incentive]
        benefits_package:
          type: array
          items:
            type: string
            enum: [health_insurance, dental_insurance, retirement_plan, meal_allowance, transport_allowance, training_budget]

    FollowUpSchedule:
      type: object
      properties:
        initial_check:
          type: string
          format: date
        probation_review:
          type: string
          format: date
        six_month_review:
          type: string
          format: date

    ComplianceMonitoring:
      type: object
      required:
        - person_id
        - monitoring_period
        - obligations
      properties:
        '@context':
          type: object
        '@type':
          type: string
          enum: ['empl:ComplianceMonitoring']
        monitoring_id:
          type: string
          format: uuid
        person_id:
          type: string
        monitoring_period:
          $ref: '#/components/schemas/DateRange'
        obligations:
          type: array
          items:
            $ref: '#/components/schemas/Obligation'
        overall_compliance_score:
          type: number
          minimum: 0.0
          maximum: 1.0
        compliance_status:
          type: string
          enum: [compliant, partial_compliance, non_compliant]
        enforcement_actions:
          type: array
          items:
            $ref: '#/components/schemas/EnforcementAction'
        next_review_date:
          type: string
          format: date
        caseworker_id:
          type: string

    Obligation:
      type: object
      required:
        - obligation_type
        - requirement
        - status
      properties:
        obligation_type:
          type: string
          enum: [job_search, pes_appointments, training_participation, reporting, skills_assessment]
        requirement:
          type: string
          description: Specific requirement description
        status:
          type: string
          enum: [compliant, non_compliant, pending]
        compliance_rate:
          type: number
          minimum: 0.0
          maximum: 1.0
        evidence:
          type: array
          items:
            $ref: '#/components/schemas/ComplianceEvidence'
        last_evidence_date:
          type: string
          format: date
        next_deadline:
          type: string
          format: date-time

    ComplianceEvidence:
      type: object
      properties:
        evidence_type:
          type: string
          enum: [job_application, interview_attendance, training_session, caseworker_meeting, skill_certificate]
        evidence_date:
          type: string
          format: date
        description:
          type: string
        verified:
          type: boolean

    EnforcementAction:
      type: object
      required:
        - action_type
        - action_date
        - reason
      properties:
        action_type:
          type: string
          enum: [warning_letter, benefit_reduction, benefit_suspension, benefit_termination, mandatory_training]
        action_date:
          type: string
          format: date
        reason:
          type: string
        severity_level:
          type: string
          enum: [low, medium, high]
        reversible:
          type: boolean

    # Request/Response Schemas
    ReferralRequest:
      type: object
      required:
        - transaction_id
        - referral_requests
      properties:
        transaction_id:
          type: string
          format: uuid
        referral_requests:
          type: array
          items:
            $ref: '#/components/schemas/ReferralRequestItem'
          minItems: 1
          maxItems: 100

    ReferralRequestItem:
      type: object
      required:
        - reference_id
        - timestamp
        - referral_data
        - consent
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        referral_data:
          type: object
          required:
            - person
            - source_programme
            - referral_reason
          properties:
            person:
              $ref: '#/components/schemas/Person'
            source_programme:
              $ref: '#/components/schemas/Programme'
            referral_reason:
              type: string
              enum: [unemployment_benefit_activation, work_capacity_assessment, compliance_requirement, training_opportunity]
            target_pes_office:
              type: string
            compliance_requirements:
              $ref: '#/components/schemas/ComplianceRequirements'
        consent:
          $ref: '#/components/schemas/Consent'
        locale:
          type: string
          pattern: '^[a-z]{2}-[A-Z]{2}$'
          description: Language and country code (e.g., es-CL)

    Consent:
      type: object
      required:
        - consent_given
        - consent_date
        - consent_type
        - legal_basis
      properties:
        consent_given:
          type: boolean
        consent_date:
          type: string
          format: date-time
        consent_type:
          type: string
          enum: [employment_services, data_sharing, training_participation]
        legal_basis:
          type: string
          description: Legal basis for data processing

    ReferralResponse:
      type: object
      required:
        - transaction_id
        - referral_responses
      properties:
        transaction_id:
          type: string
          format: uuid
        correlation_id:
          type: string
          format: uuid
        referral_responses:
          type: array
          items:
            $ref: '#/components/schemas/ReferralResponseItem'

    ReferralResponseItem:
      type: object
      required:
        - reference_id
        - timestamp
        - status
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        status:
          type: string
          enum: [succ, pdng, rjct, err]
        status_reason_code:
          type: string
        status_reason_message:
          type: string
        data:
          type: object
          properties:
            referral_id:
              type: string
              format: uuid
            pes_case_number:
              type: string
            assigned_caseworker:
              type: string
            appointment_scheduled:
              type: string
              format: date-time
            next_steps:
              type: array
              items:
                type: string
        locale:
          type: string

    ReferralUpdate:
      type: object
      properties:
        status:
          type: string
          enum: [accepted, rejected, completed]
        status_reason:
          type: string
        assigned_caseworker:
          type: string
        appointment_scheduled:
          type: string
          format: date-time
        notes:
          type: string

    ReferralSearchResponse:
      type: object
      properties:
        referrals:
          type: array
          items:
            $ref: '#/components/schemas/EmploymentReferral'
        pagination:
          $ref: '#/components/schemas/Pagination'

    EmploymentBenefitRequest:
      type: object
      required:
        - transaction_id
        - benefit_requests
      properties:
        transaction_id:
          type: string
          format: uuid
        benefit_requests:
          type: array
          items:
            $ref: '#/components/schemas/EmploymentBenefitRequestItem'
          minItems: 1
          maxItems: 50

    EmploymentBenefitRequestItem:
      type: object
      required:
        - reference_id
        - timestamp
        - benefit_data
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        benefit_data:
          type: object
          required:
            - person_id
            - benefit_type
          properties:
            person_id:
              type: string
            benefit_type:
              type: string
              enum: [training, work_opportunity, employment_subsidy]
            employment_benefit_subtype:
              type: string
            benefit_description:
              type: string
            training_details:
              $ref: '#/components/schemas/TrainingDetails'
            benefit_value:
              $ref: '#/components/schemas/Currency'
            stipend:
              $ref: '#/components/schemas/Stipend'
            attendance_requirement:
              type: number
              minimum: 0.0
              maximum: 1.0
        authorization:
          type: object
          properties:
            approved_by:
              type: string
            approval_date:
              type: string
              format: date-time
            budget_code:
              type: string

    EmploymentBenefitResponse:
      type: object
      required:
        - transaction_id
        - benefit_responses
      properties:
        transaction_id:
          type: string
          format: uuid
        correlation_id:
          type: string
          format: uuid
        benefit_responses:
          type: array
          items:
            $ref: '#/components/schemas/EmploymentBenefitResponseItem'

    EmploymentBenefitResponseItem:
      type: object
      required:
        - reference_id
        - timestamp
        - status
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        status:
          type: string
          enum: [succ, pdng, rjct, err]
        status_reason_code:
          type: string
        data:
          type: object
          properties:
            benefit_id:
              type: string
              format: uuid
            enrollment_number:
              type: string
            start_date:
              type: string
              format: date
            training_location:
              type: string
            contact_person:
              type: string
            next_steps:
              type: array
              items:
                type: string

    EmploymentBenefitUpdate:
      type: object
      properties:
        completion_status:
          type: string
          enum: [active, completed, withdrawn, suspended]
        attendance_rate:
          type: number
          minimum: 0.0
          maximum: 1.0
        performance_score:
          type: number
        completion_date:
          type: string
          format: date
        notes:
          type: string

    EmploymentBenefitSearchResponse:
      type: object
      properties:
        benefits:
          type: array
          items:
            $ref: '#/components/schemas/EmploymentBenefit'
        pagination:
          $ref: '#/components/schemas/Pagination'

    JobPlacementRequest:
      type: object
      required:
        - transaction_id
        - placement_requests
      properties:
        transaction_id:
          type: string
          format: uuid
        placement_requests:
          type: array
          items:
            $ref: '#/components/schemas/JobPlacementRequestItem'
          minItems: 1
          maxItems: 50

    JobPlacementRequestItem:
      type: object
      required:
        - reference_id
        - timestamp
        - placement_data
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        placement_data:
          type: object
          required:
            - person_id
            - employer
            - job_details
            - placement_date
          properties:
            person_id:
              type: string
            referral_id:
              type: string
              format: uuid
            employer:
              $ref: '#/components/schemas/Employer'
            job_details:
              $ref: '#/components/schemas/JobDetails'
            compensation:
              $ref: '#/components/schemas/Compensation'
            placement_date:
              type: string
              format: date
            start_date:
              type: string
              format: date
            placement_method:
              type: string
              enum: [pes_job_matching, direct_application, referral, recruitment_agency, training_placement]

    JobPlacementResponse:
      type: object
      required:
        - transaction_id
        - placement_responses
      properties:
        transaction_id:
          type: string
          format: uuid
        correlation_id:
          type: string
          format: uuid
        placement_responses:
          type: array
          items:
            $ref: '#/components/schemas/JobPlacementResponseItem'

    JobPlacementResponseItem:
      type: object
      required:
        - reference_id
        - timestamp
        - status
      properties:
        reference_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time
        status:
          type: string
          enum: [succ, pdng, rjct, err]
        status_reason_code:
          type: string
        data:
          type: object
          properties:
            placement_id:
              type: string
              format: uuid
            pes_case_updated:
              type: boolean
            sp_notifications_sent:
              type: array
              items:
                $ref: '#/components/schemas/SystemNotification'

    JobPlacementUpdate:
      type: object
      properties:
        placement_status:
          type: string
          enum: [successful, unsuccessful, ongoing]
        employment_verified:
          type: boolean
        termination_date:
          type: string
          format: date
        termination_reason:
          type: string
          enum: [resignation, termination, contract_completion, layoff]
        follow_up_notes:
          type: string
        sp_benefit_impact:
          $ref: '#/components/schemas/SPBenefitImpact'

    JobPlacementUpdateResponse:
      type: object
      properties:
        placement_id:
          type: string
          format: uuid
        status:
          type: string
          enum: [updated, failed]
        sp_notifications_sent:
          type: array
          items:
            $ref: '#/components/schemas/SystemNotification'
        next_review_date:
          type: string
          format: date

    JobPlacementSearchResponse:
      type: object
      properties:
        placements:
          type: array
          items:
            $ref: '#/components/schemas/JobPlacement'
        pagination:
          $ref: '#/components/schemas/Pagination'

    NotificationRequest:
      type: object
      required:
        - transaction_id
        - notifications
      properties:
        transaction_id:
          type: string
          format: uuid
        notifications:
          type: array
          items:
            $ref: '#/components/schemas/NotificationItem'
          minItems: 1
          maxItems: 100

    NotificationItem:
      type: object
      required:
        - reference_id
        - notification_type
        - person_id
        - source_system
        - target_system
      properties:
        reference_id:
          type: string
          format: uuid
        notification_type:
          type: string
          enum: [employment_status_change, benefit_completion, compliance_alert, placement_update, referral_update]
        person_id:
          type: string
        source_system:
          type: string
          enum: [pes, sp-mis, training-provider, employer]
        target_system:
          type: string
          enum: [pes, sp-mis, training-provider, employer]
        notification_data:
          type: object
          description: Type-specific notification payload
        priority:
          type: string
          enum: [low, normal, high, urgent]
          default: normal
        expected_response:
          type: string
          enum: [none, acknowledgment, action_required]

    NotificationResponse:
      type: object
      required:
        - transaction_id
        - notification_responses
      properties:
        transaction_id:
          type: string
          format: uuid
        notification_responses:
          type: array
          items:
            $ref: '#/components/schemas/NotificationResponseItem'

    NotificationResponseItem:
      type: object
      required:
        - reference_id
        - status
      properties:
        reference_id:
          type: string
          format: uuid
        status:
          type: string
          enum: [succ, pdng, rjct, err]
        message:
          type: string
        processed_at:
          type: string
          format: date-time
        target_system_response:
          type: object
          description: Response from target system if available

    SystemNotification:
      type: object
      properties:
        target_system:
          type: string
        notification_type:
          type: string
        sent_at:
          type: string
          format: date-time
        delivery_status:
          type: string
          enum: [sent, delivered, failed, pending_retry]

    WebhookSubscriptionRequest:
      type: object
      required:
        - url
        - event_types
      properties:
        url:
          type: string
          format: uri
          description: HTTPS URL for webhook delivery
        event_types:
          type: array
          items:
            type: string
            enum: [referral_created, referral_updated, employment_status_changed, benefit_enrolled, benefit_completed, placement_created, placement_updated, compliance_alert]
          minItems: 1
        filters:
          type: object
          description: Optional filters for webhook delivery
          properties:
            person_ids:
              type: array
              items:
                type: string
            programmes:
              type: array
              items:
                type: string
        secret:
          type: string
          description: Secret key for webhook signature verification
        description:
          type: string
          description: Human-readable description of the subscription

    WebhookSubscription:
      type: object
      required:
        - subscription_id
        - url
        - event_types
        - status
        - created_at
      properties:
        subscription_id:
          type: string
          format: uuid
        url:
          type: string
          format: uri
        event_types:
          type: array
          items:
            type: string
        filters:
          type: object
        status:
          type: string
          enum: [active, paused, disabled]
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time
        last_delivery:
          type: string
          format: date-time
        delivery_stats:
          $ref: '#/components/schemas/WebhookDeliveryStats'
        description:
          type: string

    WebhookSubscriptionUpdate:
      type: object
      properties:
        url:
          type: string
          format: uri
        event_types:
          type: array
          items:
            type: string
        filters:
          type: object
        status:
          type: string
          enum: [active, paused, disabled]
        secret:
          type: string
        description:
          type: string

    WebhookSubscriptionList:
      type: object
      properties:
        subscriptions:
          type: array
          items:
            $ref: '#/components/schemas/WebhookSubscription'
        pagination:
          $ref: '#/components/schemas/Pagination'

    WebhookDeliveryStats:
      type: object
      properties:
        total_deliveries:
          type: integer
        successful_deliveries:
          type: integer
        failed_deliveries:
          type: integer
        last_success:
          type: string
          format: date-time
        last_failure:
          type: string
          format: date-time

    # AI and Mobile Service Schemas
    AIJobMatchingRequest:
      type: object
      required:
        - person_id
        - job_preferences
      properties:
        person_id:
          type: string
          description: Unique person identifier
        person_skills:
          $ref: '#/components/schemas/PersonSkills'
        job_preferences:
          $ref: '#/components/schemas/JobPreferences'
        career_goals:
          $ref: '#/components/schemas/CareerGoals'
        geographic_constraints:
          $ref: '#/components/schemas/GeographicConstraints'
        ai_model_version:
          type: string
          default: "worknet_v2.1"
          description: AI model version to use for matching

    PersonSkills:
      type: object
      properties:
        technical_skills:
          type: array
          items:
            $ref: '#/components/schemas/Skill'
        soft_skills:
          type: array
          items:
            $ref: '#/components/schemas/Skill'
        certifications:
          type: array
          items:
            $ref: '#/components/schemas/Certification'
        work_experience:
          type: array
          items:
            $ref: '#/components/schemas/WorkExperience'
        education_level:
          type: string
          enum: [primary, secondary, vocational, bachelor, master, doctorate]

    Skill:
      type: object
      required:
        - skill_name
        - proficiency_level
      properties:
        skill_name:
          type: string
        skill_category:
          type: string
          enum: [technical, administrative, customer_service, manual_labor, creative, analytical]
        proficiency_level:
          type: string
          enum: [beginner, intermediate, advanced, expert]
        years_experience:
          type: integer
          minimum: 0
        certified:
          type: boolean

    JobPreferences:
      type: object
      properties:
        preferred_industries:
          type: array
          items:
            type: string
        employment_type:
          type: string
          enum: [full_time, part_time, contract, temporary, freelance]
        salary_expectation:
          $ref: '#/components/schemas/SalaryRange'
        work_schedule_preference:
          type: string
          enum: [regular_hours, flexible, shift_work, remote, hybrid]
        career_level:
          type: string
          enum: [entry_level, mid_level, senior_level, executive]

    AIJobMatchingResponse:
      type: object
      properties:
        transaction_id:
          type: string
          format: uuid
        person_id:
          type: string
        matching_results:
          type: array
          items:
            $ref: '#/components/schemas/JobMatch'
        career_recommendations:
          type: array
          items:
            $ref: '#/components/schemas/CareerRecommendation'
        skills_gap_analysis:
          $ref: '#/components/schemas/SkillsGapAnalysis'
        employment_sustainability_prediction:
          $ref: '#/components/schemas/SustainabilityPrediction'
        ai_confidence_score:
          type: number
          minimum: 0.0
          maximum: 1.0

    JobMatch:
      type: object
      properties:
        job_id:
          type: string
        job_title:
          type: string
        employer_name:
          type: string
        match_score:
          type: number
          minimum: 0.0
          maximum: 1.0
        match_reasons:
          type: array
          items:
            type: string
        salary_range:
          $ref: '#/components/schemas/SalaryRange'
        location:
          type: string
        employment_type:
          type: string
        skills_alignment:
          type: number
          minimum: 0.0
          maximum: 1.0
        growth_potential:
          type: string
          enum: [low, medium, high]

    SustainabilityPrediction:
      type: object
      properties:
        employment_retention_probability:
          type: number
          minimum: 0.0
          maximum: 1.0
          description: Probability of employment lasting 6+ months
        career_progression_potential:
          type: string
          enum: [low, medium, high]
        recommended_support_services:
          type: array
          items:
            type: string
            enum: [mentorship, additional_training, workplace_accommodation, transportation_support]

    MobileJobDiary:
      type: object
      properties:
        person_id:
          type: string
        diary_period:
          $ref: '#/components/schemas/DateRange'
        job_search_activities:
          type: array
          items:
            $ref: '#/components/schemas/JobSearchActivity'
        compliance_status:
          $ref: '#/components/schemas/ComplianceStatus'
        upcoming_appointments:
          type: array
          items:
            $ref: '#/components/schemas/Appointment'
        goals_progress:
          $ref: '#/components/schemas/GoalsProgress'
        mobile_app_usage:
          $ref: '#/components/schemas/MobileUsageStats'

    JobSearchActivity:
      type: object
      required:
        - activity_date
        - activity_type
      properties:
        activity_id:
          type: string
          format: uuid
        activity_date:
          type: string
          format: date
        activity_type:
          type: string
          enum: [job_application, interview_attended, networking_event, skills_training, job_fair]
        employer_name:
          type: string
        position_title:
          type: string
        application_method:
          type: string
          enum: [online, in_person, referral, recruitment_agency]
        outcome:
          type: string
          enum: [pending, interview_scheduled, rejected, offer_received]
        notes:
          type: string
        supporting_documents:
          type: array
          items:
            type: string

    MobileJobDiaryUpdate:
      type: object
      required:
        - person_id
        - activities
      properties:
        person_id:
          type: string
        activities:
          type: array
          items:
            $ref: '#/components/schemas/JobSearchActivity'
        location_data:
          $ref: '#/components/schemas/LocationData'
        app_session_info:
          $ref: '#/components/schemas/AppSessionInfo'

    LocationData:
      type: object
      properties:
        latitude:
          type: number
        longitude:
          type: number
        accuracy:
          type: number
        timestamp:
          type: string
          format: date-time
        purpose:
          type: string
          enum: [job_interview, employer_visit, training_center, pes_office]

    CountryPerformanceBenchmarks:
      type: object
      required:
        - country_code
        - reporting_period
        - metrics
      properties:
        country_code:
          type: string
          pattern: '^[A-Z]{2}$'
        country_name:
          type: string
        implementation_pattern:
          type: string
          enum: [CL-RSH-SENCE, KR-WORK24, DE-SGB, AU-JOBACTIVE, TR-ISKUR-ISAF]
        reporting_period:
          $ref: '#/components/schemas/DateRange'
        metrics:
          $ref: '#/components/schemas/PerformanceMetrics'
        integration_maturity:
          $ref: '#/components/schemas/IntegrationMaturity'
        operational_data:
          $ref: '#/components/schemas/OperationalData'

    PerformanceMetrics:
      type: object
      properties:
        referral_processing:
          type: object
          properties:
            average_processing_time_hours:
              type: number
              example: 18
            processing_time_p95_hours:
              type: number
            success_rate:
              type: number
              minimum: 0.0
              maximum: 1.0
              example: 0.987
        employment_outcomes:
          type: object
          properties:
            placement_rate_6_months:
              type: number
              minimum: 0.0
              maximum: 1.0
              example: 0.67
            retention_rate_12_months:
              type: number
              minimum: 0.0
              maximum: 1.0
            training_completion_rate:
              type: number
              minimum: 0.0
              maximum: 1.0
              example: 0.78
        system_performance:
          type: object
          properties:
            api_availability:
              type: number
              minimum: 0.0
              maximum: 1.0
            cross_system_consistency:
              type: number
              minimum: 0.0
              maximum: 1.0
              example: 0.987
            fraud_detection_rate:
              type: number
              minimum: 0.0
              maximum: 1.0
              example: 0.008
        user_satisfaction:
          type: object
          properties:
            beneficiary_rating:
              type: number
              minimum: 1.0
              maximum: 5.0
              example: 4.2
            caseworker_rating:
              type: number
              minimum: 1.0
              maximum: 5.0
            employer_rating:
              type: number
              minimum: 1.0
              maximum: 5.0

    IntegrationMaturity:
      type: object
      properties:
        overall_score:
          type: number
          minimum: 0.0
          maximum: 1.0
          example: 0.95
        maturity_level:
          type: string
          enum: [basic, developing, high, advanced]
        dimensions:
          type: object
          properties:
            digital_infrastructure:
              type: number
              minimum: 1
              maximum: 5
            legal_framework:
              type: number
              minimum: 1
              maximum: 5
            service_integration:
              type: number
              minimum: 1
              maximum: 5
            outcome_achievement:
              type: number
              minimum: 1
              maximum: 5
            innovation_adoption:
              type: number
              minimum: 1
              maximum: 5

    OperationalData:
      type: object
      properties:
        total_beneficiaries:
          type: integer
          example: 694245
        annual_referrals:
          type: integer
        annual_placements:
          type: integer
        training_budget:
          $ref: '#/components/schemas/Currency'
        staff_count:
          type: integer
        system_uptime:
          type: number
          minimum: 0.0
          maximum: 1.0

    Pagination:
      type: object
      required:
        - total_count
        - limit
        - offset
      properties:
        total_count:
          type: integer
          minimum: 0
          description: Total number of items available
        limit:
          type: integer
          minimum: 1
          description: Number of items requested
        offset:
          type: integer
          minimum: 0
          description: Number of items skipped
        has_more:
          type: boolean
          description: Whether more items are available

    # Error Schemas
    ErrorResponse:
      type: object
      required:
        - error
      properties:
        error:
          $ref: '#/components/schemas/ErrorDetail'
        correlation_id:
          type: string
          format: uuid
        timestamp:
          type: string
          format: date-time

    ErrorDetail:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
          description: Machine-readable error code
        message:
          type: string
          description: Human-readable error message
        details:
          type: array
          items:
            $ref: '#/components/schemas/ErrorField'
        documentation_url:
          type: string
          format: uri
          description: Link to error documentation

    ErrorField:
      type: object
      required:
        - field
        - error_code
        - message
      properties:
        field:
          type: string
          description: Field name that caused the error
        error_code:
          type: string
          description: Specific error code for the field
        message:
          type: string
          description: Error message for the field
        value:
          description: Invalid value that caused the error

  responses:
    # DCI Standard Responses
    StandardResponse:
      description: "Standard DCI response following Response.yaml pattern"
      content:
        application/json:
          schema:
            type: object
            properties:
              message:
                type: string
                description: "Response message"
              timestamp:
                type: string
                format: date-time
                description: "Response timestamp"

    HttpErrorResponse:
      description: "HTTP layer error details following DCI HttpErrorResponse.yaml"
      content:
        application/json:
          schema:
            type: object
            description: "HTTP transport layer error codes. Used by components like gateways, LB responding with HTTP status codes 1xx, 2xx, 3xx, 4xx and 5xx"
            properties:
              errors:
                type: array
                items:
                  type: object
                  properties:
                    code:
                      type: string
                      description: "error code"
                    message:
                      type: string
                      description: "error message"

    BadRequest:
      description: Invalid request format or missing required fields
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            validation_error:
              summary: Validation error example
              value:
                error:
                  code: "VALIDATION_ERROR"
                  message: "Request validation failed"
                  details:
                    - field: "person.identifiers"
                      error_code: "REQUIRED_FIELD_MISSING"
                      message: "At least one identifier is required"
                      value: null

    Unauthorized:
      description: Invalid or missing authentication token
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            invalid_token:
              summary: Invalid token example
              value:
                error:
                  code: "INVALID_TOKEN"
                  message: "The provided authentication token is invalid or expired"

    Forbidden:
      description: Insufficient permissions for the requested operation
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            insufficient_scope:
              summary: Insufficient scope example
              value:
                error:
                  code: "INSUFFICIENT_SCOPE"
                  message: "Token does not have required scope: referral:create"

    NotFound:
      description: Requested resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            resource_not_found:
              summary: Resource not found example
              value:
                error:
                  code: "RESOURCE_NOT_FOUND"
                  message: "Referral with ID 550e8400-e29b-41d4-a716-446655440000 not found"

    Conflict:
      description: Request conflicts with current resource state
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            duplicate_referral:
              summary: Duplicate referral example
              value:
                error:
                  code: "DUPLICATE_REFERRAL"
                  message: "A referral for this person already exists in pending status"

    UnprocessableEntity:
      description: Valid format but business rule violations
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            business_rule_violation:
              summary: Business rule violation example
              value:
                error:
                  code: "BUSINESS_RULE_VIOLATION"
                  message: "Person is not assessed as work-able and cannot be referred to PES"

    TooManyRequests:
      description: Rate limit exceeded
      headers:
        Retry-After:
          description: Number of seconds to wait before retrying
          schema:
            type: integer
        X-RateLimit-Limit:
          description: Request limit per time window
          schema:
            type: integer
        X-RateLimit-Remaining:
          description: Remaining requests in current window
          schema:
            type: integer
        X-RateLimit-Reset:
          description: Time when rate limit resets (Unix timestamp)
          schema:
            type: integer
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            rate_limit_exceeded:
              summary: Rate limit exceeded example
              value:
                error:
                  code: "RATE_LIMIT_EXCEEDED"
                  message: "Request rate limit exceeded. Please retry after 60 seconds."

    InternalServerError:
      description: Internal server error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
          examples:
            internal_error:
              summary: Internal server error example
              value:
                error:
                  code: "INTERNAL_SERVER_ERROR"
                  message: "An unexpected error occurred. Please contact support with correlation ID."

tags:
  - name: Referrals
    description: Employment referral operations between SP and PES systems
  - name: Employment Status
    description: Employment status verification for benefit compliance
  - name: Employment Benefits
    description: Training and work opportunity benefit management
  - name: Job Placements
    description: Job placement tracking and outcome reporting
  - name: Compliance Monitoring
    description: Employment obligation compliance tracking
  - name: Notifications
    description: Cross-system notifications and alerts
  - name: Webhooks
    description: Real-time event subscription management
```

---

## JSON Schema Validation

The API specification includes comprehensive JSON Schema validation for all request and response objects, ensuring data quality and consistency across Employment-SP integrations.

### Key Validation Rules

1. **UUID Format Validation**: All ID fields use UUID v4 format validation
2. **Date/Time Format**: ISO 8601 format required for all temporal data
3. **Currency Precision**: Decimal string format preserves monetary precision
4. **Enumeration Values**: Strict validation against predefined code directories
5. **Required Fields**: Mandatory field validation at schema level
6. **Array Constraints**: Min/max items validation for batch operations
7. **String Patterns**: Regex validation for country codes, language codes, etc.

### Business Rule Validation

Beyond schema validation, the API implements business rule validation:

- Work capacity assessment required before referral acceptance
- Consent verification for employment service participation
- Geographic jurisdiction validation for PES office assignments
- Income thresholds for SP benefit eligibility determination
- Training completion requirements for benefit continuation

---

## Security Considerations

### OAuth2 Scopes

The API uses fine-grained OAuth2 scopes for authorization:

- `referral:create`, `referral:read`, `referral:update` - Referral management
- `employment:read` - Employment status access
- `benefit:manage`, `benefit:read` - Employment benefit operations
- `placement:create`, `placement:read`, `placement:update` - Job placement management
- `compliance:read` - Compliance monitoring access
- `notification:send` - Cross-system notifications
- `webhook:manage`, `webhook:read` - Webhook subscription management

### Data Protection

- **PII Minimization**: Only necessary data fields included in API responses
- **Field-level Encryption**: Sensitive identifiers encrypted in transit and at rest
- **Audit Logging**: All API operations logged with purpose of use
- **Consent Tracking**: Explicit consent verification for data sharing
- **Retention Policies**: Automated data lifecycle management

### Rate Limiting

- **Standard Operations**: 1,000 requests/hour per client
- **Batch Operations**: 100 items maximum per request
- **Real-time Notifications**: 10,000 requests/hour for high-volume integrations
- **Webhook Deliveries**: Exponential backoff retry logic

---

## Integration Patterns

### Synchronous Operations

- **Real-time Verification**: Employment status checks for immediate benefit decisions
- **Interactive Referrals**: Real-time referral creation with immediate PES response
- **Compliance Queries**: On-demand compliance status verification

### Asynchronous Operations

- **Batch Processing**: Large-scale referral creation and benefit enrollment
- **Background Verification**: Periodic employment status updates
- **Webhook Delivery**: Event-driven notifications for status changes

### Error Handling

- **Graceful Degradation**: Partial success handling for batch operations
- **Retry Logic**: Exponential backoff for transient failures
- **Circuit Breakers**: System protection during high error rates
- **Fallback Mechanisms**: Alternative workflows when integrations are unavailable

---

## Monitoring and Observability

### API Metrics

- **Request Volume**: Requests per second by endpoint and client
- **Response Times**: P50, P95, P99 latency percentiles
- **Error Rates**: Error rates by type and severity
- **Success Rates**: Successful operation completion rates

### Business Metrics

- **Referral Success Rate**: Percentage of referrals successfully processed
- **Employment Verification Accuracy**: Confidence level distribution
- **Benefit Completion Rate**: Training and program completion statistics
- **Placement Sustainability**: Long-term employment outcome tracking

### SLA Definitions

- **Availability**: 99.9% uptime for production services
- **Response Time**: 95% of requests completed within 2 seconds
- **Throughput**: Support for 10,000 concurrent operations
- **Data Freshness**: Employment status updates within 24 hours

---

`TODO(implementation)`: Generate client SDKs for major programming languages (Java, Python, JavaScript, C#).

`TODO(testing)`: Create comprehensive test suites including integration test scenarios and sample data sets.

`TODO(documentation)`: Develop implementation guides with step-by-step integration instructions and best practices.