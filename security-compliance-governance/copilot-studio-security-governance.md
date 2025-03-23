# Microsoft Copilot Studio Security and Governance Documentation

## Table of Contents

1. [Introduction](#introduction)
2. [Data Loss Prevention (DLP) Policies](#data-loss-prevention-dlp-policies)
   - [DLP Policy Overview](#dlp-policy-overview)
   - [Creating Copilot Studio-Specific DLP Policies](#creating-copilot-studio-specific-dlp-policies)
   - [Managing Connector Access](#managing-connector-access)
   - [Custom Connector Controls](#custom-connector-controls)
   - [Data Exfiltration Prevention](#data-exfiltration-prevention)
   - [DLP Policy Monitoring and Enforcement](#dlp-policy-monitoring-and-enforcement)
3. [Encryption](#encryption)
   - [Data at Rest Encryption](#data-at-rest-encryption)
   - [Data in Transit Encryption](#data-in-transit-encryption)
   - [Key Management](#key-management)
   - [Customer Managed Keys (CMK)](#customer-managed-keys-cmk)
4. [Data Retention Policies](#data-retention-policies)
   - [Default Retention Settings](#default-retention-settings)
   - [Conversation Data Retention](#conversation-data-retention)
   - [Analytics Data Retention](#analytics-data-retention)
   - [Custom Retention Policies](#custom-retention-policies)
   - [Data Deletion Processes](#data-deletion-processes)
5. [Data Masking for Sensitive Information](#data-masking-for-sensitive-information)
   - [Built-in Data Masking Features](#built-in-data-masking-features)
   - [Entity Redaction](#entity-redaction)
   - [Implementing Custom Data Masking](#implementing-custom-data-masking)
   - [Data Masking in Transcripts and Analytics](#data-masking-in-transcripts-and-analytics)
6. [PII/PHI Handling Procedures](#piiphi-handling-procedures)
   - [Identifying PII/PHI in Copilot Studio](#identifying-piiphi-in-copilot-studio)
   - [Compliance Frameworks](#compliance-frameworks)
   - [PII/PHI Data Flow Management](#piiphi-data-flow-management)
   - [User Consent Management](#user-consent-management)
   - [Incident Response for Data Breaches](#incident-response-for-data-breaches)
7. [Implementation Best Practices](#implementation-best-practices)
8. [Appendix](#appendix)
   - [Regulatory Compliance References](#regulatory-compliance-references)
   - [Microsoft Documentation Resources](#microsoft-documentation-resources)

## Introduction

Microsoft Copilot Studio provides organizations with the ability to create powerful conversational AI assistants, often handling sensitive business data and customer information. This documentation focuses on the security and governance aspects of Microsoft Copilot Studio, with particular emphasis on data protection, privacy controls, and compliance capabilities.

As conversational AI systems become more integrated with business processes, the need for robust security and governance frameworks becomes increasingly important. This guide serves as a comprehensive resource for IT security teams, compliance officers, and Copilot Studio administrators seeking to implement appropriate controls for their AI assistants.

## Data Loss Prevention (DLP) Policies

### DLP Policy Overview

Data Loss Prevention (DLP) policies in Microsoft Copilot Studio help organizations control the flow of sensitive information within and outside the organization's boundaries. These policies are managed through the Power Platform admin center and apply specifically to Copilot Studio environments.

DLP policies in Copilot Studio focus primarily on:

1. Controlling access to connectors that may transmit sensitive data
2. Preventing unauthorized data exfiltration through conversations
3. Limiting the distribution of sensitive information across environments
4. Managing data sharing between Copilot Studio and other applications

### Creating Copilot Studio-Specific DLP Policies

To create DLP policies specifically for Copilot Studio:

1. Navigate to the [Power Platform Admin Center](https://admin.powerplatform.microsoft.com/)
2. Select **Policies** > **Data policies**
3. Click **+ New policy**
4. Name your policy and provide a description
5. Define the environments where the policy will apply
6. Configure connector classifications (see next section)
7. Review and create the policy

Example policy creation:

```
Policy Name: Copilot Studio Production Environment DLP
Description: Controls data flow for customer service Copilots
Environments: Production, UAT
Connectors: [Configured as described below]
```

### Managing Connector Access

Connectors in Copilot Studio are classified into three groups:

1. **Business data only**: Approved for handling sensitive business data
2. **No business data allowed**: Not approved for sensitive data
3. **Blocked**: Completely unavailable for use

For Copilot Studio implementations, recommended connector configurations include:

| Connector Type | Classification | Rationale |
|----------------|---------------|-----------|
| Microsoft Dataverse | Business data only | Primary data store for Copilot Studio |
| SharePoint | Business data only | Knowledge base storage |
| Microsoft 365 services | Business data only | Integration with workplace tools |
| HTTP with Azure AD | Business data only | Secure API connections |
| Azure OpenAI | Business data only | AI model integration |
| Generic HTTP | No business data allowed | Potential data exfiltration risk |
| Social media connectors | No business data allowed | Public exposure risk |
| Email connectors | Business data only | With strict monitoring |
| Custom connectors | Case by case evaluation | Depends on implementation |

To configure these classifications:

1. In your DLP policy, select **Connector classification**
2. Move connectors between categories according to your security requirements
3. Pay special attention to any connectors that might process PII/PHI
4. Save your configuration

### Custom Connector Controls

When implementing custom connectors for Copilot Studio:

1. Register the custom connector in the Power Platform admin center
2. Document data flows and security measures for the connector
3. Classify the connector in your DLP policy based on risk assessment
4. Implement additional monitoring for custom connector usage
5. Review custom connector security regularly

Example custom connector documentation:

```
Connector Name: HR System Connector
Purpose: Retrieves employee information for HR Copilot
Data Processed: Employee ID, Name, Department (no PII beyond name)
Authentication: Azure AD with application permissions
DLP Classification: Business data only
Additional Controls: Audit logging enabled, response data masked
```

### Data Exfiltration Prevention

Copilot Studio conversations can potentially be used to extract sensitive information. To prevent data exfiltration:

1. Implement topic-specific entity masking (see Data Masking section)
2. Configure sensitive information detection in Generative AI responses
3. Implement connection security for external systems
4. Use environment isolation for different security boundaries
5. Enable conversation monitoring for sensitive information patterns

Technical implementation example:

```
# Power Automate flow to monitor for potential data exfiltration
trigger:
  - When a conversation is completed
actions:
  - Scan transcript for sensitive patterns
  - If matches found:
      - Log security event
      - Notify security team
      - Store transcript with enhanced controls
```

### DLP Policy Monitoring and Enforcement

To ensure DLP policies are effective:

1. **Monitoring**:
   - Review DLP policy violation reports in Power Platform admin center
   - Configure alerts for policy violations
   - Regularly audit connector usage across environments

2. **Enforcement**:
   - DLP policies are enforced at design time and runtime
   - Violations prevent deployment of Copilots using blocked connectors
   - Runtime violations trigger security alerts and can block operations

3. **Audit trail**:
   - Configure Unified Audit Logging for Copilot Studio activities
   - Export audit logs to SIEM systems for centralized monitoring
   - Maintain audit records for compliance purposes

Example audit configuration:

```
# Audit Settings Configuration
Audit Log Retention: 90 days
Critical Events Monitored:
  - DLP policy modifications
  - Connector access attempts
  - New connection creation
  - Admin role changes
Integration: Azure Sentinel via Log Analytics workspace
```

## Encryption

### Data at Rest Encryption

Microsoft Copilot Studio encrypts all data at rest using Microsoft's service encryption technologies. This includes:

1. **Dataverse Storage**: Copilot configurations, topics, and entities are stored in Microsoft Dataverse, which uses transparent data encryption with Microsoft-managed keys by default.

2. **Knowledge Base Content**: Content stored in SharePoint or other knowledge sources maintains those services' encryption methods.

3. **Conversation History**: Stored conversations are encrypted in Dataverse with the same level of protection as other data.

4. **Analytics Data**: Usage analytics and reporting data are encrypted at rest.

The encryption implementation uses:

- AES-256 bit encryption
- Microsoft-managed keys by default
- Optional customer-managed keys (see CMK section)

Service encryption is applied automatically and requires no specific configuration by the customer beyond optional CMK setup.

### Data in Transit Encryption

All data transmitted to and from Copilot Studio is encrypted using industry-standard Transport Layer Security (TLS) protocols:

1. **External Communications**:
   - All connections between users and Copilot Studio use TLS 1.2 or higher
   - Web client connections enforce HTTPS
   - Connections from mobile apps use encrypted channels

2. **Internal Service Communications**:
   - Service-to-service communication within Microsoft data centers is encrypted
   - Connections to external APIs use TLS
   - Authentication tokens are transmitted securely

3. **Configuration**:
   - TLS is enforced by default
   - Minimum TLS version can be configured at the tenant level
   - Weak cipher suites are disabled

Implementation details:

```
# TLS Configuration
Minimum Version: TLS 1.2
Preferred Cipher Suites:
  - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
  - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
Certificate Validation: Enabled
HSTS: Implemented for web endpoints
```

### Key Management

Encryption key management in Copilot Studio follows Microsoft's standard practices:

1. **Microsoft-managed keys (Default)**:
   - Microsoft generates, manages, and rotates encryption keys
   - Keys are stored in Azure Key Vault with strict access controls
   - Key rotation occurs automatically according to Microsoft policies
   - No customer configuration required

2. **Key Rotation**:
   - Microsoft-managed keys are rotated automatically based on Microsoft policies
   - Customer-managed keys can be rotated according to customer policies
   - Service continuity is maintained during key rotation

3. **Key Access Controls**:
   - Keys are protected by Microsoft's internal security controls
   - Access to keys is strictly limited and audited
   - Keys are backed up securely to prevent data loss

### Customer Managed Keys (CMK)

For organizations requiring maximum control over encryption keys, Customer Managed Keys can be implemented:

1. **Prerequisites**:
   - Azure Key Vault in your Azure subscription
   - Proper key vault access policies
   - RSA 2048-bit keys (minimum)

2. **Implementation Steps**:
   ```
   # Implementation Process
   1. Create an Azure Key Vault if not already available
   2. Generate or import a suitable RSA key
   3. Configure access policies for Power Platform services
   4. Enable CMK for Dataverse in Power Platform admin center
   5. Associate the key with your Copilot Studio environment
   6. Verify encryption status
   ```

3. **Operational Considerations**:
   - Key deletion will make data inaccessible
   - Key rotation requires careful planning
   - Audit key access regularly
   - Maintain backup procedures for keys

4. **Limitations**:
   - CMK is applied at the Dataverse environment level
   - Some transient data may still use Microsoft-managed keys
   - Preview features may not support CMK initially

## Data Retention Policies

### Default Retention Settings

Microsoft Copilot Studio implements default data retention periods for different types of data:

1. **Conversation Data**:
   - Chat transcripts: 30 days by default
   - Conversation metadata: 90 days by default
   - Conversation analytics: 13 months by default

2. **System Data**:
   - Copilot configuration data: Retained until deletion
   - Topic and entity definitions: Retained until deletion
   - Knowledge base references: Retained until deletion

3. **Audit Data**:
   - Admin actions: 1 year by default
   - System activity logs: 180 days by default
   - Security logs: 1 year by default

These default settings can be modified through custom retention policies within the constraints of Microsoft's platform capabilities.

### Conversation Data Retention

Conversation data retention can be configured at multiple levels:

1. **Environment-Level Configuration**:
   ```
   # Environment Retention Settings
   Navigate to: Power Platform Admin Center > Environments > [Your Environment]
   Select: Settings > Product > Copilot Studio > Data Retention
   Configure: Conversation transcript retention period
   ```

2. **Copilot-Level Configuration**:
   ```
   # Copilot-Specific Retention
   Navigate to: Copilot Studio > [Your Copilot] > Settings > Conversation logs
   Configure: Custom retention period (15-90 days)
   ```

3. **Channel-Specific Considerations**:
   - Teams channel: Additional retention governed by Teams policies
   - Website chat: Follows Copilot Studio policies
   - Custom channels: May require additional configuration

Important considerations:
- Shorter retention periods reduce risk but limit analytics capabilities
- Regulatory requirements may dictate minimum retention periods
- Legal holds may override default retention policies

### Analytics Data Retention

Analytics data provides insights into Copilot performance but may contain sensitive information:

1. **Usage Analytics**:
   - Engagement metrics: 13 months by default
   - Session information: 13 months by default
   - Performance analytics: 13 months by default

2. **Configuration Options**:
   ```
   # Analytics Retention Configuration
   Navigate to: Power Platform Admin Center > Analytics settings
   Configure: Data retention periods for analytics
   Options: 30 days, 90 days, 6 months, 13 months
   ```

3. **Data Minimization**:
   - Configure anonymization for analytics data
   - Use aggregated data where possible
   - Apply role-based access controls to analytics dashboards

### Custom Retention Policies

For organizations with specific regulatory or internal requirements:

1. **Define Custom Policies**:
   - Document specific retention requirements by data category
   - Identify regulatory drivers for retention periods
   - Consider geography-specific requirements

2. **Implementation Approaches**:
   ```
   # Custom Implementation Options
   
   # Option 1: Platform Configuration
   - Configure standard retention policies to maximum extent possible
   - Implement custom export and deletion processes for specialized requirements
   
   # Option 2: Custom Process Automation
   - Create Power Automate flows to export and archive data based on custom schedules
   - Implement automated deletion workflows
   - Maintain audit trail of retention activities
   ```

3. **Documentation Requirements**:
   - Maintain clear documentation of custom retention policies
   - Record justification for retention periods
   - Document technical implementation
   - Maintain audit trail of policy changes

### Data Deletion Processes

When data reaches the end of its retention period:

1. **Automatic Deletion**:
   - System automatically deletes data based on configured retention periods
   - Deletion is permanent and non-recoverable
   - Deletion occurs gradually to minimize system impact

2. **Manual Deletion**:
   - Administrators can manually delete conversation data
   - Process: Copilot Studio > Analytics > Conversation history > Delete
   - Deletion requests are processed within 30 days

3. **Deletion Verification**:
   ```
   # Verification Process
   1. Review conversation analytics to confirm data removal
   2. Check storage metrics for expected reduction
   3. Document deletion completion for compliance purposes
   4. Maintain deletion records according to retention policy
   ```

4. **Backup Considerations**:
   - Ensure backup policies align with retention policies
   - Implement processes to delete data from backups when required
   - Document backup retention and deletion procedures

## Data Masking for Sensitive Information

### Built-in Data Masking Features

Microsoft Copilot Studio provides several built-in mechanisms for masking sensitive data:

1. **Entity Masking**:
   - System automatically recognizes and masks common PII entities
   - Built-in entities include: credit card numbers, social security numbers, phone numbers, email addresses
   - Masked data appears as `****` in transcripts and logs

2. **Configuration**:
   ```
   # Configuring Built-in Masking
   Navigate to: Copilot Studio > [Your Copilot] > Settings > Security
   Enable: PII entity masking
   Select: Entities to mask automatically
   ```

3. **Masking Scope**:
   - User inputs: Masked in real-time
   - Conversation transcripts: Masked before storage
   - Analytics: Masked in reports and dashboards
   - Exports: Masked in exported data

### Entity Redaction

For more advanced control over sensitive information:

1. **Custom Entity Recognition**:
   - Define custom entities for business-specific sensitive data
   - Create pattern recognition for custom formats
   - Configure masking behavior for custom entities

2. **Implementation Steps**:
   ```
   # Custom Entity Redaction
   
   1. Define entity patterns:
      Navigate to: Copilot Studio > Settings > Entities
      Create: New custom entity
      Define: Regular expression pattern
   
   2. Configure redaction:
      Set: Redaction type (full or partial)
      Set: Replacement character
      Enable: For conversation and analytics
   ```

3. **Partial Redaction Options**:
   - First/last character preservation
   - Pattern-based masking (e.g., X-XXX-XXXX for phone numbers)
   - Context-aware masking (different for display vs. storage)

### Implementing Custom Data Masking

For scenarios requiring advanced data masking beyond built-in capabilities:

1. **Pre-processing Approach**:
   ```
   # Power Automate Flow for Pre-processing
   
   trigger:
     - When message received before processing
   actions:
     - Identify sensitive patterns using regex
     - Replace sensitive data with masked values
     - Forward processed message to Copilot
   ```

2. **Post-processing Approach**:
   ```
   # Power Automate Flow for Post-processing
   
   trigger:
     - Before saving conversation transcript
   actions:
     - Scan for sensitive patterns
     - Apply masking rules
     - Save sanitized transcript
   ```

3. **Advanced Pattern Recognition**:
   - Implement NLP-based entity recognition for complex patterns
   - Use Azure AI services for enhanced recognition
   - Maintain pattern database for organization-specific sensitive data

Example custom masking pattern for employee IDs:

```
# Custom Employee ID Masking

Pattern: EMP-\d{5}
Replacement: EMP-XXXXX
Implementation: Power Automate flow with Regular Expression processing
Scope: All conversation inputs and outputs
```

### Data Masking in Transcripts and Analytics

Ensuring sensitive data is properly masked in historical records:

1. **Transcript Masking**:
   - Real-time masking during conversation
   - Secondary validation before storage
   - Masked representation in conversation history

2. **Analytics Considerations**:
   - Ensure masked data in dashboard reports
   - Restrict access to raw conversation data
   - Apply additional masking for exported analytics

3. **Audit and Verification**:
   ```
   # Masking Verification Process
   
   1. Regular sampling of conversation transcripts
   2. Automated scanning for unmasked PII patterns
   3. Remediation process for identified issues
   4. Documentation of verification activities
   ```

4. **Edge Cases**:
   - Handle multi-format representations (e.g., dates in various formats)
   - Address language-specific PII patterns
   - Manage context where partial information might be combined

## PII/PHI Handling Procedures

### Identifying PII/PHI in Copilot Studio

Before implementing controls, organizations should identify where PII/PHI may exist in their Copilot Studio implementation:

1. **Data Inventory**:
   - User inputs in conversations
   - Data retrieved from connected systems
   - Information stored in knowledge bases
   - Authentication and context information
   - Conversation history and transcripts
   - Analytics and reporting data

2. **Classification Framework**:
   | Data Type | Examples | Risk Level | Handling Requirements |
   |-----------|----------|------------|------------------------|
   | Direct identifiers | Names, IDs, contact info | High | Full masking, strict access controls |
   | Indirect identifiers | ZIP codes, birth dates | Medium | Contextual controls |
   | Sensitive attributes | Health info, financial data | High | Encryption, limited access |
   | Authentication data | Credentials, tokens | Critical | Secure storage, no logging |

3. **Documentation Requirements**:
   - Maintain data inventory for all PII/PHI
   - Document data flows showing PII/PHI transmission
   - Update inventory when new topics or connections are added

### Compliance Frameworks

PII/PHI handling in Copilot Studio should align with relevant compliance frameworks:

1. **Regulatory Considerations**:
   - GDPR (European Union)
   - HIPAA (US healthcare)
   - CCPA/CPRA (California)
   - Industry-specific regulations

2. **Microsoft Compliance Features**:
   - Microsoft 365 Compliance Center integration
   - Compliance score assessments
   - Compliance Manager automation

3. **Documentation**:
   ```
   # Compliance Documentation Template
   
   Regulation: [Specific Regulation]
   Applicable Requirements:
     - [Requirement 1]
     - [Requirement 2]
   Implementation Controls:
     - Technical: [List of technical controls]
     - Administrative: [List of process controls]
     - Physical: [List of physical controls]
   Verification Methods:
     - [Audit procedures]
     - [Testing methodology]
   ```

### PII/PHI Data Flow Management

Managing the complete lifecycle of sensitive data:

1. **Data Collection Controls**:
   - Implement just-in-time collection principles
   - Provide clear notice before collecting PII/PHI
   - Confirm necessity before requesting sensitive information

2. **Processing Controls**:
   ```
   # Processing Controls Implementation
   
   1. Minimize data retention in memory
   2. Process sensitive data in isolated environments
   3. Apply masking before storage
   4. Implement least privilege access to processing systems
   ```

3. **Storage Controls**:
   - Apply appropriate encryption (see Encryption section)
   - Implement data segregation for highest sensitivity data
   - Apply access controls based on data classification

4. **Transmission Controls**:
   - Ensure TLS for all data transmission
   - Implement additional payload encryption for highly sensitive data
   - Validate secure channel before transmitting PII/PHI

5. **Disposal Controls**:
   - Implement secure deletion procedures
   - Verify removal from all systems including backups
   - Maintain deletion records for compliance

### User Consent Management

Managing user consent for PII/PHI collection and processing:

1. **Consent Collection**:
   ```
   # Consent Implementation in Copilot Studio
   
   1. Create initial greeting topic with privacy notice
   2. Provide clear explanation of data usage
   3. Request explicit consent
   4. Store consent in conversation variable
   5. Check consent before collecting sensitive information
   ```

2. **Consent Management**:
   - Allow users to withdraw consent
   - Implement consent expiration policies
   - Maintain audit trail of consent activities

3. **Example Consent Flow**:
   ```
   Copilot: "Welcome to [Service]. I may need to collect personal information to help you. 
            You can review our privacy policy at [link]. 
            Do you consent to share necessary personal information?"
   
   User: "Yes"
   
   Copilot: "Thank you. I'll only collect information needed to assist you.
            You can say 'stop collecting my data' at any time."
   
   [Conversation variable 'userConsent' set to 'true' with timestamp]
   ```

### Incident Response for Data Breaches

Preparing for potential data breach scenarios:

1. **Breach Detection**:
   - Implement monitoring for unusual data access patterns
   - Configure alerts for potential data leakage
   - Establish escalation procedures

2. **Response Plan**:
   ```
   # Incident Response Steps
   
   1. Containment:
      - Isolate affected systems
      - Disable compromised access points
      - Document initial findings
   
   2. Assessment:
      - Determine data affected
      - Identify scope of exposure
      - Document timeline
   
   3. Notification:
      - Alert internal stakeholders
      - Prepare external notifications
      - Contact Microsoft if service-related
   
   4. Remediation:
      - Address technical vulnerabilities
      - Enhance controls
      - Document remediation steps
   ```

3. **Documentation Requirements**:
   - Maintain incident response runbooks
   - Document breach notification procedures
   - Conduct regular response drills
   - Update procedures based on lessons learned

## Implementation Best Practices

To effectively implement the security and governance controls outlined in this document:

1. **Phased Approach**:
   - Start with foundational DLP and encryption controls
   - Implement data masking for most sensitive use cases first
   - Gradually enhance with advanced controls
   - Regularly review and improve implementation

2. **Cross-Functional Collaboration**:
   - Involve security, compliance, legal, and business stakeholders
   - Clearly define responsibilities for each control area
   - Establish regular review cadence

3. **Documentation and Training**:
   - Maintain comprehensive security documentation
   - Train developers and administrators on security features
   - Create user guidance for appropriate data sharing
   - Document technical implementation details

4. **Monitoring and Improvement**:
   - Implement regular security reviews
   - Conduct penetration testing for critical Copilots
   - Track security metrics and set improvement goals
   - Stay current with Microsoft security updates

5. **Recommended Security Configuration Baseline**:

   ```
   # Copilot Studio Security Baseline
   
   Environment Configuration:
   - Separate development/test/production environments
   - Production restricted to necessary admin accounts only
   - Environment-specific DLP policies
   
   Authentication Controls:
   - MFA required for all admin access
   - Conditional access policies applied
   - Regular access reviews
   
   Data Controls:
   - Default entity masking enabled
   - Custom masking for organization-specific PII
   - Minimum necessary conversation retention
   - Regular transcript auditing
   
   Integration Security:
   - All custom connectors reviewed for security
   - API connections use managed identities where possible
   - Regular rotation of API keys and secrets
   
   Monitoring:
   - Unified audit logging enabled
   - SIEM integration for security events
   - Automated scanning for PII leakage
   ```

## Appendix

### Regulatory Compliance References

The following references provide additional information on regulatory requirements relevant to Copilot Studio implementations:

1. **GDPR (General Data Protection Regulation)**
   - [Official GDPR Text](https://gdpr-info.eu/)
   - [Microsoft GDPR Documentation](https://www.microsoft.com/en-us/trust-center/privacy/gdpr-overview)

2. **HIPAA (Health Insurance Portability and Accountability Act)**
   - [HHS HIPAA Guidance](https://www.hhs.gov/hipaa/for-professionals/index.html)
   - [Microsoft HIPAA Documentation](https://www.microsoft.com/en-us/trust-center/compliance/hipaa)

3. **CCPA/CPRA (California Consumer Privacy Act/California Privacy Rights Act)**
   - [California Privacy Laws](https://oag.ca.gov/privacy/ccpa)
   - [Microsoft CCPA Documentation](https://www.microsoft.com/en-us/trust-center/privacy/ccpa-faq)

4. **Industry-Specific Regulations**
   - Financial Services: [GLBA](https://www.ftc.gov/business-guidance/privacy-security/gramm-leach-bliley-act)
   - Education: [FERPA](https://www2.ed.gov/policy/gen/guid/fpco/ferpa/index.html)

### Microsoft Documentation Resources

For the most current information on Copilot Studio security and compliance features, refer to these Microsoft resources:

1. **Copilot Studio Documentation**
   - [Microsoft Copilot Studio Security Overview](https://learn.microsoft.com/en-us/microsoft-copilot-studio/security)
   - [Data Handling in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/data-handling)

2. **Power Platform Security**
   - [Power Platform DLP Policies](https://learn.microsoft.com/en-us/power-platform/admin/wp-data-loss-prevention)
   - [Dataverse Security](https://learn.microsoft.com/en-us/power-platform/admin/wp-security)

3. **Microsoft Compliance**
   - [Microsoft Trust Center](https://www.microsoft.com/en-us/trust-center)
   - [Microsoft Compliance Offerings](https://learn.microsoft.com/en-us/compliance/regulatory/offering-home)

4. **Security Best Practices**
   - [Microsoft Security Best Practices](https://learn.microsoft.com/en-us/security/compass/microsoft-security-compass-introduction)
   - [Power Platform Security Planning](https://learn.microsoft.com/en-us/power-platform/guidance/planning/security)
