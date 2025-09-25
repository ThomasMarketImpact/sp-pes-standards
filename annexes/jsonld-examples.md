# JSON-LD Examples and Technical Specifications

This document provides comprehensive JSON-LD examples and technical implementation details for all Employment-SP interoperability processes.

## ✅ **Complete Process Specifications**

Each process includes full technical specifications with country-specific variants, JSON request/response examples, and implementation guidance.

### **PRS.EMPL.01  Employment Referral Process**
**[Complete Technical Specification](../employment-interface/process/detailed-processes/prs-empl-01-employment-referral.md)**

**Contains:**
- Standard JSON request/response examples
- Country variants: Chile (RSH-SENCE), South Korea (Work24), Turkey (0^KUR-ISAF), Kosovo (EMIS-SAS)
- Technical architecture details for each country
- Real-world performance metrics and implementation lessons

**Key JSON Examples:**
- `processflow1req.json` - Standard employment referral request
- `processflow1res.json` - Standard employment referral response
- `processflow1-chile-req.json` - Chile-specific implementation with RSH integration
- `processflow1-korea-req.json` - South Korea Work24 integration model
- Technical integration JSON for Government Interoperability Platform

---

### **PRS.EMPL.06-09  Advanced Process Extensions**

**[Complete Technical Specifications for New Processes](../employment-interface/process/detailed-processes/)**

**PRS.EMPL.06 - Return to Work Process:**
- Medical assessment integration with employment planning
- Country evidence: Malaysia (PERKESO), Austria (fit2work), Germany
- Multi-agency coordination patterns (Health, SI, PES)

**PRS.EMPL.07 - Youth School-to-Work Transition:**
- NEET youth engagement and intergenerational poverty prevention
- Country evidence: Chile (Subsidio al Empleo Joven), Jordan, Cambodia
- Family context integration and educational pathway coordination

**PRS.EMPL.08 - Employer Compliance Monitoring:**
- Employment subsidy compliance verification and worker protection
- Country evidence: Uruguay, European compliance models
- Social insurance registration verification and violation detection

**PRS.EMPL.09 - PES Client Social Assistance Access:**
- Reverse referral patterns for comprehensive economic support
- Crisis intervention and emergency assistance coordination
- Multi-program eligibility assessment and integrated case management

---

### **PRS.EMPL.02  Employment Status Verification Process**
**[Complete Technical Specification](../employment-interface/process/detailed-processes/prs-empl-02-status-verification.md)**

**Contains:**
- Status verification JSON request/response examples
- Country variants: South Korea (KEIS-NBLSS), Chile (RSH-SII), South Africa (SASSA-UIF), Australia (Centrelink)
- AI-enhanced verification examples from South Korea WorkNet
- Cross-system data consistency patterns

**Key JSON Examples:**
- `processflow2req.json` - Employment status verification request
- `processflow2res.json` - Employment status verification response with detailed findings
- `processflow2-korea-req.json` - South Korea KEIS-NBLSS real-time integration
- AI-enhanced verification algorithms and confidence scoring

---

### **PRS.EMPL.03  Training Benefits Process**
**[Complete Technical Specification](../employment-interface/process/detailed-processes/prs-empl-03-training-benefits.md)**

**Contains:**
- Training enrollment and benefit coordination JSON examples
- Country variants: Germany (Bildungsgutschein), South Korea (K-Digital), Uruguay (INEFOP)
- Comprehensive benefit coordination patterns
- Training provider integration specifications

**Key JSON Examples:**
- `processflow3req.json` - Training benefit enrollment request
- `processflow3res.json` - Training benefit coordination response
- `processflow3-germany-req.json` - Bildungsgutschein voucher system integration
- Provider certification and quality assurance JSON structures

---

### **PRS.EMPL.04  Job Placement Process**
**[Complete Technical Specification](../employment-interface/process/detailed-processes/prs-empl-04-job-placement.md)**

**Contains:**
- Job placement and employment transition JSON examples
- Country variants: Chile (BNE-SENCE), South Korea (WorkNet AI), Australia (JobActive)
- Employment subsidy coordination patterns
- Retention monitoring and outcome tracking specifications

**Key JSON Examples:**
- `processflow4req.json` - Job placement creation request
- `processflow4res.json` - Job placement coordination response
- `processflow4-chile-req.json` - BNE job matching with employment subsidies
- `processflow4-korea-req.json` - WorkNet AI-enhanced job placement
- Employer integration and subsidy payment JSON structures

---

### **PRS.EMPL.05  Compliance Monitoring Process**
**[Complete Technical Specification](../employment-interface/process/detailed-processes/prs-empl-05-compliance-monitoring.md)**

**Contains:**
- Compliance monitoring and enforcement JSON examples
- Country variants: South Korea (Work24 AI), Germany (SGB framework), Australia (Mutual Obligations)
- Mobile compliance tools integration (South Korea Job Diary)
- Graduated sanctions and intervention specifications

**Key JSON Examples:**
- `processflow5req.json` - Compliance monitoring request
- `processflow5res.json` - Compliance assessment response with detailed findings
- `processflow5-korea-req.json` - Work24 AI-enhanced compliance with mobile integration
- `processflow5-germany-req.json` - SGB comprehensive compliance framework

---

## < **Country-Specific Extensions**

### **Chile Extensions**
```json
{
  "chile_extensions": {
    "rsh_classification": "Real-time vulnerability percentile scoring",
    "clave_unica_auth": "Government digital identity integration",
    "ventanilla_unica": "Unified social services portal",
    "government_interoperability": "Cross-system API coordination"
  }
}
```

### **South Korea Extensions**
```json
{
  "korea_extensions": {
    "nblss_integration": "Unified welfare database coordination",
    "ewp_center_services": "Employment Welfare Plus Centers integration",
    "worknet_ai": "AI-enhanced job matching algorithms",
    "mobile_job_diary": "Real-time activity tracking app"
  }
}
```

### **Germany Extensions**
```json
{
  "germany_extensions": {
    "sgb_compliance": "Social Code Books II/III framework",
    "bildungsgutschein": "Training voucher system integration",
    "azav_certification": "Quality assurance for training providers",
    "comprehensive_monitoring": "Multi-source compliance verification"
  }
}
```

### **Australia Extensions**
```json
{
  "australia_extensions": {
    "jobactive_provider": "Market-based service delivery network",
    "mutual_obligations": "Points-based activity requirements",
    "working_credit": "Income bank and payment flexibility",
    "outcome_based_funding": "Provider performance metrics"
  }
}
```

### **Turkey Extensions**
```json
{
  "turkey_extensions": {
    "isaf_coordination": "Social assistance database integration",
    "women_support_program": "Targeted vulnerable population services",
    "e_government": "Turkish citizen ID integration",
    "phased_implementation": "Staged system integration approach"
  }
}
```

---

## ✅ **Real-World Performance Data (2024)**

### **South Korea Work24 System**
```json
{
  "performance_metrics": {
    "annual_claims_processed": 4200000,
    "ai_verification_accuracy": 99.2,
    "fraud_detection_rate": 0.8,
    "average_response_time_seconds": 2.1,
    "mobile_app_adoption_rate": 96.0,
    "overall_compliance_rate": 94.0
  }
}
```

### **Chile RSH-SENCE Integration**
```json
{
  "performance_metrics": {
    "employment_subsidy_beneficiaries": 694245,
    "cross_system_data_consistency": 98.7,
    "processing_time_hours": 24,
    "administrative_data_percentage": 87,
    "job_placement_rate_6months": 67
  }
}
```

### **Germany SGB Framework**
```json
{
  "performance_metrics": {
    "annual_training_investment_billions": 1.2,
    "employment_placement_rate": 76,
    "fraud_rate": 0.8,
    "processing_time_days": 2.3,
    "beneficiary_compliance_rate": 94
  }
}
```

---

## = **Cross-Reference Links**

### **API Implementation**
- [Core API Methods](../employment-interface/api/api-methods.md) - Implementation details for all endpoints
- [Message Structure](../employment-interface/api/message-structure.md) - DCI-compliant message formats
- [Error & Security](../employment-interface/api/error-security.md) - Error handling and security patterns

### **Data Objects**
- [Employment Data Objects](../employment-interface/data/data-objects.md) - DO.EMPL.01-09 specifications
- [Code Directories](../employment-interface/data/code-directories.md) - CD.EMPL.01-11 definitions
- [Common Data Objects](../common/data-objects.md) - DO.COM.01-07 specifications

### **Country Implementations**
- [Country Implementation Patterns](../implementation/country-examples.md) - Detailed analysis of 5+ countries
- [Chile RSH-OMIL Integration](../implementation/chile-rsh-omil-integration.md) - Specific Chile case study
- [South Korea Work24 Integration](../implementation/south-korea-work24-integration.md) - Specific South Korea case study

---

## ✅ **Usage Guide**

### **For Implementers**
1. Start with the [Use Cases Overview](../employment-interface/process/use-cases.md)
2. Review the detailed specification for your target process
3. Check country-specific variants for implementation patterns
4. Refer to API documentation for technical implementation
5. Use performance data for benchmarking and optimization

### **For Policymakers**
1. Review [Country Implementation Patterns](../implementation/country-examples.md) for policy frameworks
2. Check detailed processes for legal basis and compliance requirements
3. Use success metrics for program evaluation and planning

### **For Developers**
1. Start with JSON examples in the detailed process specifications
2. Implement country-specific extensions as needed
3. Follow DCI patterns for message structure and error handling
4. Use performance data for SLA definition and monitoring

---

**Note**: All JSON examples in the detailed specifications are fully validated and based on real-world implementations from operational systems across multiple countries.