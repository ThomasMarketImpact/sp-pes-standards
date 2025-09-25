# Employment-SP Code Directories

This document defines standardized enumerations specific to Employment-Social Protection interoperability, extending DCI common patterns.

## CD.EMPL.01  EmploymentStatus

**Description**: Extended employment status enumeration for PES-SP integration, building on DCI Social EmploymentStatusEnum.

**Enumeration**:
| Code | Description | ILO Category | SP Benefit Impact |
|------|-------------|--------------|-------------------|
| `employed` | Employed (any type) | 1 | Benefit graduation review |
| `not_employed` | Not employed/unemployed | 2 | Benefit continuation |
| `seeking_work` | Actively seeking employment | 2 | PES activation required |
| `underemployed` | Working below capacity/hours | 1 | Partial benefit eligibility |
| `temporary_layoff` | Temporarily laid off | 2 | Temporary benefit activation |
| `self_employed` | Self-employment (any type) | 1 | Income verification required |
| `paid_employee` | Paid employment as employee | 1 | Full benefit suspension |
| `employer` | Self-employed with employees | 1 | Benefit ineligible |
| `own_account_worker` | Self-employed without employees | 1 | Income-based assessment |
| `cooperative_member` | Member of producers' cooperative | 1 | Income verification required |
| `family_worker` | Contributing family worker | 1 | Partial income assessment |
| `unable_to_work` | Unable to work (health/disability) | 3 | Disability benefit referral |
| `retired` | Retired from workforce | 3 | Pension benefit coordination |
| `student` | Full-time student | 3 | Education benefit coordination |
| `caregiver` | Primary caregiver responsibilities | 3 | Care allowance coordination |

**Usage notes**:
- Follows ILO employment status classification standards
- SP benefit impact guides automatic benefit adjustments
- Status changes trigger cross-system notifications
- Regular verification required for benefit-impacting statuses

---

## CD.EMPL.02  ReferralReason

**Description**: Reasons for referring SP beneficiaries to PES services.

**Enumeration**:
| Code | Description | Urgency | PES Service Type |
|------|-------------|---------|------------------|
| `unemployment_benefit_activation` | Activation for unemployment benefits | High | Job search support |
| `conditionality_enforcement` | SP benefit conditionality requirement | High | Compliance monitoring |
| `work_ability_assessment` | Assessment of work capacity | Medium | Skills assessment |
| `skills_mismatch` | Skills don't match available jobs | Medium | Training referral |
| `job_placement_assistance` | Direct job placement support | Medium | Job matching |
| `career_counseling` | Career guidance and planning | Low | Counseling services |
| `entrepreneurship_support` | Self-employment development | Low | Business development |
| `disability_accommodation` | Work accommodation for disability | Medium | Inclusive employment |
| `youth_activation` | Youth employment activation | Medium | Youth programs |
| `long_term_unemployment` | Extended unemployment period | High | Intensive support |
| `benefit_graduation` | Transition off SP benefits | Medium | Sustainable employment |
| `seasonal_employment` | Seasonal work opportunities | Low | Temporary placement |

**Usage notes**:
- Referral reason determines PES service pathway
- Urgency level affects appointment scheduling priority
- Multiple reasons allowed per referral with primary designation
- Tracks effectiveness of different referral types

---

## CD.EMPL.03  BenefitType

**Description**: Employment-related benefit types, extending IBR BenefitTypeEnum.

**Enumeration**:
| Code | Description | Source System | Duration Type |
|------|-------------|---------------|---------------|
| `cash` | Direct cash transfer | SP-MIS | Ongoing |
| `voucher` | Voucher/conditional transfer | SP-MIS | Limited |
| `in_kind` | In-kind benefits | SP-MIS | Variable |
| `training` | Skills training programs | PES | Fixed term |
| `work_opportunity` | Employment opportunities | PES | Variable |
| `insurance` | Employment insurance | Insurance | Ongoing |
| `vocational_training` | Vocational skills development | PES/Training | Fixed term |
| `job_readiness` | Job preparation training | PES | Short term |
| `digital_literacy` | Digital skills training | PES/Education | Short term |
| `entrepreneurship` | Business development support | PES/Development | Medium term |
| `apprenticeship` | Work-based learning | PES/Employer | Long term |
| `wage_subsidy` | Employer wage supplementation | PES/Labor | Medium term |
| `transport_allowance` | Transportation support | PES | Ongoing |
| `childcare_support` | Childcare while working/training | SP-MIS | Variable |
| `equipment_provision` | Work tools/equipment | PES | One-time |

**Usage notes**:
- Source system indicates responsible agency
- Duration type affects benefit administration
- Multiple benefit types can be combined for comprehensive support
- Benefit effectiveness tracked for program evaluation

---

## CD.EMPL.04  EmploymentType

**Description**: Types of employment arrangements for verification and tracking.

**Enumeration**:
| Code | Description | Contract Security | Income Stability |
|------|-------------|-------------------|------------------|
| `permanent_full_time` | Permanent full-time employment | High | High |
| `permanent_part_time` | Permanent part-time employment | High | Medium |
| `temporary_full_time` | Temporary full-time contract | Medium | Medium |
| `temporary_part_time` | Temporary part-time contract | Medium | Low |
| `seasonal` | Seasonal employment | Low | Variable |
| `casual` | Casual/irregular work | Low | Low |
| `fixed_term` | Fixed-term contract | Medium | Medium |
| `probationary` | Employment on probation | Medium | Medium |
| `apprenticeship` | Apprenticeship position | Medium | Low |
| `internship` | Internship/work placement | Low | Low |
| `freelance` | Freelance/consultant work | Low | Variable |
| `gig_economy` | Platform/gig work | Low | Variable |
| `zero_hours` | Zero-hours contract | Low | Low |
| `agency_work` | Employment through agency | Low | Variable |

**Usage notes**:
- Contract security affects SP benefit eligibility
- Income stability influences benefit calculation
- Employment type determines verification requirements
- Used for labor market analysis and policy development

---

## CD.EMPL.05  TrainingType

**Description**: Categories of employment-related training and skills development.

**Enumeration**:
| Code | Description | Duration | Certification |
|------|-------------|----------|---------------|
| `vocational_skills` | Technical/trade skills training | 3-12 months | Industry certificate |
| `digital_literacy` | Basic computer and digital skills | 1-3 months | Skills certificate |
| `job_readiness` | Work preparation and soft skills | 2-6 weeks | Completion certificate |
| `entrepreneurship` | Business development skills | 2-6 months | Business certificate |
| `language_training` | Language skills for employment | 3-12 months | Language certificate |
| `financial_literacy` | Personal finance management | 1-2 months | Completion certificate |
| `industry_specific` | Sector-specific skills training | 1-6 months | Industry certificate |
| `leadership_development` | Management and leadership skills | 3-6 months | Leadership certificate |
| `safety_certification` | Workplace safety training | 1-4 weeks | Safety certificate |
| `professional_development` | Career advancement skills | 1-3 months | Professional certificate |
| `apprenticeship_training` | Formal apprenticeship program | 1-4 years | Trade qualification |
| `university_pathway` | Higher education preparation | 6-12 months | Education certificate |
| `disability_specific` | Training for persons with disabilities | Variable | Adapted certificate |
| `youth_programs` | Youth-specific skill development | 2-6 months | Youth certificate |

**Usage notes**:
- Duration indicates typical program length
- Certification type affects employment prospects
- Training effectiveness measured by employment outcomes
- Coordinates with national qualification frameworks

---

## CD.EMPL.06  VerificationSource

**Description**: Sources for employment status verification.

**Enumeration**:
| Code | Description | Reliability | Update Frequency |
|------|-------------|-------------|------------------|
| `employer_declaration` | Employer-provided information | High | Real-time |
| `payroll_system` | Electronic payroll integration | Very High | Monthly |
| `tax_authority` | Tax administration records | Very High | Monthly |
| `social_security` | Social security contributions | Very High | Monthly |
| `labor_ministry` | Labor ministry databases | High | Monthly |
| `self_declaration` | Individual self-reporting | Medium | As reported |
| `pes_verification` | PES caseworker verification | High | As verified |
| `third_party_service` | External verification service | Medium | Variable |
| `bank_records` | Banking/financial records | High | Monthly |
| `union_records` | Trade union membership | Medium | Quarterly |
| `professional_body` | Professional association | Medium | Annually |
| `unemployment_office` | Unemployment registration | High | Weekly |
| `field_verification` | Physical workplace verification | High | As conducted |
| `digital_platform` | Online platform integration | Medium | Real-time |

**Usage notes**:
- Reliability level affects verification confidence
- Update frequency determines data freshness
- Multiple sources can be used for cross-validation
- Verification method documented for audit purposes

---

## CD.EMPL.07  JobCategory

**Description**: Standardized job categories for placement and matching.

**Enumeration**:
| Code | Description | Skill Level | Growth Potential |
|------|-------------|-------------|------------------|
| `management` | Management and executive roles | High | High |
| `professional` | Professional and technical roles | High | High |
| `technician` | Technicians and associate professionals | Medium | Medium |
| `clerical` | Clerical and administrative | Medium | Low |
| `sales_service` | Sales and customer service | Medium | Medium |
| `agriculture` | Agricultural and fishing workers | Low | Low |
| `crafts_trades` | Skilled trades and crafts | Medium | Medium |
| `machine_operators` | Machine and equipment operators | Medium | Low |
| `elementary` | Elementary occupations | Low | Low |
| `armed_forces` | Military and security | Medium | Medium |
| `healthcare` | Healthcare and social work | High | High |
| `education` | Education and training | High | Medium |
| `arts_culture` | Arts, culture, and media | Medium | Medium |
| `transport` | Transport and logistics | Medium | Medium |
| `construction` | Construction and building | Medium | Medium |
| `hospitality` | Hospitality and tourism | Low | Medium |
| `retail` | Retail and wholesale | Low | Low |
| `manufacturing` | Manufacturing and production | Medium | Low |
| `technology` | Information technology | High | Very High |
| `finance` | Finance and insurance | High | Medium |

**Usage notes**:
- Based on international occupation classification (ISCO)
- Skill level affects training requirements
- Growth potential influences career counseling
- Used for labor market matching and planning

---

## CD.EMPL.08  PlacementMethod

**Description**: Methods used for job placement and employment matching.

**Enumeration**:
| Code | Description | Success Rate | Resource Intensity |
|------|-------------|--------------|-------------------|
| `pes_job_matching` | PES automated job matching | Medium | Low |
| `caseworker_referral` | Personal caseworker referral | High | High |
| `employer_direct` | Direct employer contact | High | Medium |
| `job_fair` | Job fair participation | Medium | Medium |
| `online_platform` | Online job platform | Medium | Low |
| `networking_events` | Professional networking | High | Medium |
| `training_placement` | Post-training job placement | Very High | High |
| `apprenticeship_conversion` | Apprenticeship to employment | Very High | High |
| `supported_employment` | Supported employment program | High | Very High |
| `social_enterprise` | Social enterprise placement | Medium | High |
| `public_sector` | Public sector recruitment | Medium | Medium |
| `temporary_agency` | Temporary employment agency | Medium | Low |
| `headhunter_agency` | Professional recruitment agency | High | Low |
| `self_placement` | Self-directed job search | Low | Very Low |

**Usage notes**:
- Success rate indicates historical placement effectiveness
- Resource intensity affects service delivery costs
- Multiple methods can be used simultaneously
- Method effectiveness varies by target population

---

## CD.EMPL.09  ComplianceStatus

**Description**: Compliance status for employment-related obligations.

**Enumeration**:
| Code | Description | Benefit Impact | Enforcement Action |
|------|-------------|----------------|-------------------|
| `fully_compliant` | Meeting all obligations | None | None |
| `partial_compliance` | Meeting some obligations | Warning | Counseling |
| `non_compliant` | Not meeting obligations | Reduction | Formal warning |
| `excused_absence` | Justified non-compliance | None | None |
| `under_review` | Compliance being assessed | Temporary hold | Investigation |
| `improvement_plan` | On compliance improvement plan | Conditional | Monitoring |
| `suspended` | Benefits suspended for non-compliance | Full suspension | Suspension |
| `terminated` | Benefits terminated | Termination | Case closure |
| `appealing` | Appealing compliance decision | Temporary hold | Appeal process |
| `reinstated` | Compliance restored | Full restoration | Resumed services |

**Usage notes**:
- Compliance status affects benefit payments directly
- Enforcement actions follow graduated approach
- Status changes require caseworker approval
- Appeal process available for disputed decisions

---

## CD.EMPL.10  ObligationType

**Description**: Types of employment-related obligations for SP beneficiaries.

**Enumeration**:
| Code | Description | Frequency | Monitoring Method |
|------|-------------|-----------|-------------------|
| `job_search` | Active job search requirement | Weekly | Evidence submission |
| `pes_appointments` | Mandatory PES meetings | Monthly | Attendance tracking |
| `training_participation` | Required training attendance | Daily | Attendance records |
| `skills_assessment` | Periodic skills evaluation | Quarterly | Assessment results |
| `work_availability` | Availability for work | Ongoing | Declaration |
| `geographic_mobility` | Willingness to relocate | As needed | Agreement |
| `application_quota` | Minimum job applications | Weekly | Application log |
| `interview_attendance` | Attend referred interviews | As scheduled | Feedback reports |
| `cv_maintenance` | Keep CV updated | Monthly | CV review |
| `benefit_reporting` | Report benefit changes | As occurs | Declaration |
| `employment_notification` | Report employment changes | Within 5 days | Notification |
| `income_declaration` | Declare all income sources | Monthly | Documentation |
| `health_certification` | Maintain work fitness | Annually | Medical certificate |
| `childcare_arrangement` | Secure childcare for work | As needed | Arrangement proof |

**Usage notes**:
- Frequency indicates how often compliance is checked
- Monitoring method determines evidence requirements
- Obligations can be adjusted based on individual circumstances
- Non-compliance triggers enforcement procedures

---

## Open Issues

`TODO(evidence)`: Validate enumeration values against country-specific employment and SP legal frameworks.

`TODO(mapping)`: Create mapping tables between national classifications and these standardized codes.

`TODO(localization)`: Develop translation matrices for multi-language support in target countries.