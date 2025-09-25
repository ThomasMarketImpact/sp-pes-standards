# Glossary

This comprehensive glossary defines technical terms, acronyms, and domain-specific concepts used throughout the Employment-Social Protection interoperability standards. Terms are organized alphabetically with cross-references to related concepts.

---

## A

### **API (Application Programming Interface)**
A set of protocols, tools, and definitions that allows different software applications to communicate with each other. In the context of Employment-SP integration, APIs enable real-time data exchange between PES and SP systems.

### **ASM (Assumptions)**
A labeling prefix used to identify foundational assumptions in the DCI standards framework. Examples include ASM.COM.01 (Data Minimization Principle) and ASM.EMPL.01 (Work Capacity and Referral Eligibility).

### **ATO (Australian Taxation Office)**
Australia's principal revenue collection agency, providing employment income verification data for SP benefit eligibility assessments in the JobActive system.

### **Authentication**
The process of verifying the identity of a user, device, or system before allowing access to protected resources. The DCI standards require OAuth2 with JWT tokens for all API access.

### **Authorization**
The process of determining what actions an authenticated user or system is permitted to perform. Uses role-based access control with fine-grained permissions per API endpoint.

### **Active Labour Market Programmes (ALMPs)**
Employment-focused interventions including job search assistance, vocational training, work experience programs, and employment subsidies designed to help unemployed individuals return to work.

### **AZAV Certification**
German quality assurance framework for vocational training providers (Akkreditierungs- und Zulassungsverordnung Arbeitsf�rderung). Required for training programs funded through Bildungsgutschein vouchers.

---

## B

### **BA (Federal Employment Agency - Germany)**
Germany's federal employment agency (Bundesagentur f�r Arbeit) responsible for employment services, vocational training, and unemployment benefits under the SGB framework.

### **Beneficiary**
An individual receiving social protection benefits or services. In Employment-SP integration, refers to work-able beneficiaries eligible for referral to employment services.

### **Bildungsgutschein**
German training voucher system providing up to �15,000 for certified vocational training programs, with continued unemployment benefit eligibility during training period.

### **BNE (Bolsa Nacional de Empleo)**
Chile's National Employment Exchange, an online job matching platform integrated with SENCE employment services and RSH socioeconomic targeting.

### **BPMN (Business Process Model and Notation)**
A standardized graphical notation for modeling business processes. Used in the Employment-SP standards to illustrate workflow sequences and decision points.

### **BR (Business Rule)**
Specific validation logic and operational constraints governing Employment-SP processes. Numbered systematically (e.g., BR.EMPL.01.01 for Work Capacity Assessment rules).

---

## C

### **CD (Code Directory)**
A labeling prefix for standardized enumerations and code lists. Examples include CD.COM.01 (RequestStatus) and CD.EMPL.01 (EmploymentStatus).

### **Centrelink**
Australia's service delivery agency for social security payments and services, integrated with JobActive providers for mutual obligations compliance monitoring.

### **Clave�nica**
Chile's unified digital identity system enabling single sign-on across government services including RSH, SENCE, and other SP-PES integrated systems.

### **COM (Common)**
A scope identifier indicating standards applicable across all DCI interoperability interfaces, not limited to Employment-SP integration.

### **Compliance Monitoring**
Systematic tracking of beneficiary adherence to employment-related obligations such as job search requirements, training participation, and reporting obligations.

### **Consent Management**
The processes and systems for obtaining, documenting, and managing individual consent for personal data processing and sharing between SP and PES systems.

### **Correlation ID**
A unique identifier (UUID v4) used to trace related transactions across multiple system boundaries, enabling end-to-end request tracking and debugging.

---

## D

### **Data Governance**
The overall management of data availability, usability, integrity, and security within Employment-SP systems, including privacy protection and retention policies.

### **Data Minimization**
The principle of limiting data collection and processing to what is strictly necessary for the specific purpose, as mandated by data protection regulations.

### **DCI (Digital Convergence Initiative)**
A framework under USP2030 to accelerate universal social protection through digital transformation and system interoperability standards.

### **DO (Data Object)**
A labeling prefix for standardized data structures. Examples include DO.COM.01 (Person) and DO.EMPL.01 (Employment Referral).

### **DTF (Data Types & Formats)**
A labeling prefix for technical specifications of data representation. Examples include DTF.COM.01 (DateTime Format) and DTF.COM.02 (Currency Format).

---

## E

### **EMPL (Employment)**
A scope identifier indicating standards specific to Employment-Social Protection interoperability, as distinct from common DCI standards.

### **Employment Insurance**
A social insurance program providing temporary income support to unemployed workers, often integrated with PES services for activation and job search requirements.

### **Employment Status Verification**
The process of confirming an individual's current employment situation through authoritative sources such as tax authorities, employers, or social security systems.

### **Employment Welfare Plus Centers**
South Korea's integrated service delivery model providing co-located employment, training, and social welfare services through unified case management.

### **EIS (Employment Insurance Scheme)**
South Korea's contributory unemployment protection system (고용보험) providing job-seeking allowances and employment promotion benefits, tightly integrated with Work24 platform.

### **Encryption**
The process of encoding data to prevent unauthorized access. DCI standards require TLS 1.3+ for data in transit and AES-256 for data at rest.

---

## F

### **FONASA (Fondo Nacional de Salud)**
Chile's National Health Fund providing health insurance verification data for employment status confirmation in SP-PES integrated systems.

### **fit2work**
Austria's preventive and return-to-work services providing coordinated support across health insurance, PES, and employers for workplace reintegration.

---

## G

### **GitBook**
The documentation platform used to publish Employment-SP interoperability standards, synchronized with the GitHub repository for version control.

### **Government Interoperability Platform**
Chile's technical infrastructure enabling real-time API integration and data sharing across government agencies including RSH, SENCE, SII, and AFC.

### **Graduation**
The process of transitioning beneficiaries off social protection benefits upon achieving employment stability, typically involving graduated benefit reduction over 3-4 months.

---

## H

### **HATEOAS (Hypermedia as the Engine of Application State)**
RESTful API design principle including resource links for navigation, implemented in DCI Employment-SP APIs for improved discoverability.

### **HRD-Korea**
South Korea's human resource development agency responsible for vocational training integration with Work24 platform and Employment Welfare Plus Centers.

### **HRD-Net**
South Korea's vocational training platform integrated into Work24, providing self-directed job skills development and education programs.

### **HTTP Status Codes**
Standardized response codes indicating the outcome of API requests. DCI standards define specific usage patterns for 2xx (success), 4xx (client errors), and 5xx (server errors).

---

## I

### **IBR (Individual Benefit Registry)**
A DCI interface pattern extended by Employment-SP standards for benefit management and coordination between social protection and employment systems.

### **Identity Resolution**
The process of matching and linking individual records across different systems using multiple identifiers while maintaining privacy and accuracy.

### **ILO (International Labour Organization)**
United Nations agency providing research, standards, and evidence base for Employment-SP integration patterns analyzed in the country implementation examples.

### **0^KUR**
Turkey's Public Employment Service Agency providing employment services, vocational training, and job placement coordination with ISAF social assistance programs.

### **ISAF (Integrated Social Assistance System)**
Turkey's unified social assistance system providing cash transfers and social services, integrated with 0^KUR for employment activation of beneficiaries.

### **ISO 8601**
International standard for date and time representation (YYYY-MM-DDTHH:mm:ss.sssZ) required for all timestamp fields in DCI Employment-SP data objects.

---

## J

### **JobActive**
Australia's employment services model using contracted providers with outcome-based funding, integrated with Centrelink for mutual obligations compliance monitoring.

### **Job Placement**
The process of matching beneficiaries with employment opportunities and tracking employment outcomes, including placement support and stability monitoring.

### **Job Diary (취업드림수첩)**
South Korea's mobile application integrated with Work24 platform for real-time job search activity tracking and compliance monitoring with 96% adoption rate.

### **JSON-LD (JSON for Linked Data)**
A JSON-based format for structuring linked data, used in DCI Employment-SP standards for semantic interoperability and context definitions.

### **JWT (JSON Web Token)**
A compact, URL-safe token format used for OAuth2 authentication in DCI APIs, containing claims about the authenticated party and their permissions.

---

## K

### **KEIS (Korea Employment Information Service)**
South Korea's employment information system providing job placement services, integrated with NBLSS through Work24 platform for unified service delivery.

### **KPI (Key Performance Indicator)**
Quantifiable measures used to evaluate the effectiveness of Employment-SP integration processes, such as referral processing time and job placement rates.

---

## L

### **Legal Basis**
The lawful foundation for processing personal data, required for all cross-system data sharing in Employment-SP integration (consent, legal obligation, or legitimate interest).

---

## M

### **Mermaid**
A markdown-based diagramming language used in the repository to create sequence diagrams, flowcharts, and architectural diagrams for Employment-SP workflows.

### **Mutual Obligations**
Australia's requirement for welfare recipients to undertake activities like job search, training, or work experience in exchange for payment, monitored through JobActive providers.

---

## N

### **National ID**
A government-issued unique identifier for citizens, used as a primary identifier for person resolution across Employment-SP systems while maintaining privacy protections.

### **NBLSS (National Basic Livelihood Security System)**
South Korea's social welfare system providing basic income support, integrated with KEIS employment services through Work24 platform and Employment Welfare Plus Centers.

### **NESP (National Employment Support Programme)**
South Korea's comprehensive employment and livelihood support program for disadvantaged groups, integrated into Work24 platform for unified service delivery.

### **NEET (Not in Education, Employment, or Training)**
Youth classification for individuals aged 15-29 who are not engaged in education, employment, or training, often targeted through specialized Employment-SP integration programs.

---

## O

### **OAuth2**
An authorization framework enabling secure API access through token-based authentication, required for all Employment-SP interoperability endpoints.

### **OMIL (Oficinas Municipales de Informaci�n Laboral)**
Chile's network of 315 municipal employment information offices providing local access to BNE job matching and SENCE training services.

### **OpenAPI**
A specification format for describing REST APIs, used to document Employment-SP API endpoints with examples and validation rules.

---

## P

### **PES (Public Employment Services)**
Government agencies responsible for employment services, job placement, skills training, and labor market intermediation for job seekers and employers.

### **Person**
A core data object (DO.COM.01) representing individuals in social protection and employment systems, with standardized fields for identification, contact, and demographic information.

### **Previred**
Chile's social security information system providing employment history and contribution data for benefit eligibility verification in SP-PES integration.

### **PRS (Process Standards)**
A labeling prefix for standardized business processes. Examples include PRS.EMPL.01 (Employment Referral) and PRS.COM.01 (OAuth2 Authentication Flow).

### **PERKESO**
Malaysia's Social Security Organization providing employment injury insurance and return-to-work programs with coordinated employment services integration.

---

## R

### **Rate Limiting**
The practice of controlling API usage by restricting the number of requests per time period, implemented to ensure fair usage and system stability.

### **Referral**
The process of directing work-able social protection beneficiaries to appropriate employment services, tracked through standardized data objects and workflows.

### **Request/Response Correlation**
The practice of linking API requests and responses using correlation identifiers for end-to-end traceability and debugging support.

### **REST (Representational State Transfer)**
An architectural style for web services emphasizing stateless communication, standard HTTP methods, and resource-based URLs, used in DCI Employment-SP APIs.

### **RSH (Registro Social de Hogares)**
Chile's Social Household Registry providing socioeconomic classification and vulnerability scoring for targeting employment services and social protection benefits.

---

## S

### **SASSA (South African Social Security Agency)**
South Africa's social security agency providing social grants, coordinated with UIF employment status verification for benefit eligibility management.

### **SENCE (Servicio Nacional de Capacitaci�n y Empleo)**
Chile's National Training and Employment Service providing skills training, employment subsidies, and job placement services integrated with RSH targeting.

### **SGB (Social Code Books)**
Germany's comprehensive legal framework (Sozialgesetzbuch) governing social security, unemployment benefits, and employment services with detailed compliance requirements.

### **SII (Servicio de Impuestos Internos)**
Chile's Internal Revenue Service providing employment income verification data for SP benefit eligibility assessment and compliance monitoring.

### **Skills Assessment**
The evaluation of an individual's abilities, competencies, and training needs to match them with appropriate employment opportunities or training programs.

### **SP-MIS (Social Protection Management Information System)**
Information systems used by social protection agencies to manage beneficiary records, benefit payments, and program administration.

### **Status Verification**
The process of confirming current employment status through multiple authoritative sources to ensure accurate benefit eligibility and compliance monitoring.

### **SEJ (Subsidio al Empleo Joven)**
Chile's youth employment subsidy targeting workers aged 18-24 from vulnerable households, providing approximately 30% wage supplementation through SENCE.

### **SMT (Subsidio al Empleo de la Mujer Trabajadora)**
Chile's women's employment subsidy (also known as Bono al Trabajo de la Mujer) targeting women aged 25-59 from vulnerable households, administered through SENCE with RSH targeting.

### **Social Assistance**
Non-contributory social protection programs providing cash transfers, vouchers, or in-kind benefits to vulnerable populations, often with work requirements for able-bodied beneficiaries.

---

## T

### **Training Benefits**
Employment-related benefits including vocational training, skills development, and associated allowances provided through coordinated SP-PES service delivery.

---

## U

### **UIF (Unemployment Insurance Fund)**
South Africa's unemployment insurance system providing temporary income support with employment status coordination through SASSA social grant systems.

### **UUID (Universally Unique Identifier)**
A 128-bit identifier guaranteed to be unique across systems, used for referral IDs, transaction IDs, and other unique identifiers in DCI standards.

### **USP2030**
The global partnership for universal social protection by 2030, under which the Digital Convergence Initiative develops interoperability standards.

---

## V

### **Verification Confidence**
A quantitative measure (0.0-1.0) indicating the reliability of employment status verification based on source quality, data freshness, and cross-verification results.

### **Versioning**
The practice of managing API changes through semantic versioning (MAJOR.MINOR.PATCH) with backward compatibility guarantees and migration support.

### **Vulnerability Percentile**
Numerical ranking system (typically 0-100) indicating relative socioeconomic vulnerability within a population, used by systems like Chile's RSH for program targeting.

---

## W

### **Webhook**
A method for real-time notifications where systems automatically send HTTP requests to registered endpoints when specific events occur, enabling reactive integration patterns.

### **Work24**
South Korea's unified employment platform integrating 9 previously separate systems for employment insurance, job matching, training enrollment, and benefit coordination.

### **WorkNet**
South Korea's AI-enhanced job matching system providing skills-based matching algorithms and employment outcome prediction within the Work24 platform.

### **Work-able**
A classification indicating that a social protection beneficiary is capable of working and eligible for referral to employment services, based on capacity assessment.

---

## Cross-References

### **Country Implementation Systems**

**Chile**: RSH (Social Household Registry), SENCE (Employment Service), BNE (Job Exchange), AFC (Unemployment Insurance), OMIL (Municipal Offices), SII (Tax Authority), Clave�nica (Digital Identity)

**South Korea**: Work24 (Unified Platform), KEIS (Employment Information), NBLSS (Basic Livelihood Security), HRD-Korea (Training), Employment Welfare Plus Centers, WorkNet (AI Matching)

**Germany**: BA (Federal Employment Agency), SGB (Social Code Framework), Bildungsgutschein (Training Vouchers), AZAV (Quality Certification)

**Australia**: Centrelink (Benefits Agency), JobActive (Provider Network), ATO (Tax Office), myGovID (Digital Identity)

**Turkey**: İŞKUR (Employment Service), ISAF (Social Assistance), e-Government Integration

### **Technical Standards**

**Authentication**: OAuth2, JWT, ClaveÚnica, myGovID
**Data Formats**: JSON-LD, ISO 8601, UUID v4, Currency (ISO 4217)
**API Patterns**: REST, HATEOAS, Rate Limiting, Webhooks
**Security**: TLS 1.3+, AES-256, HMAC-SHA256, Audit Logging

### **Process Flows**

**PRS.EMPL.01**: Employment Referral
**PRS.EMPL.02**: Status Verification
**PRS.EMPL.03**: Training Benefits
**PRS.EMPL.04**: Job Placement
**PRS.EMPL.05**: Compliance Monitoring
**PRS.EMPL.06**: Return to Work
**PRS.EMPL.07**: Youth School-to-Work Transition
**PRS.EMPL.08**: Employer Compliance Monitoring
**PRS.EMPL.09**: PES Client Social Assistance Access

### **Data Objects**

**DO.EMPL.01**: Employment Referral
**DO.EMPL.02**: Employment Status Verification
**DO.EMPL.03**: Employment Benefit
**DO.EMPL.04**: Job Placement
**DO.EMPL.05**: Compliance Monitoring

---

*This glossary is maintained as part of the Employment-SP interoperability standards and is updated with each release to reflect new concepts and terminology.*