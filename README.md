# Globalmed Project: Privacy & Compliance Architecture

*Disclaimer: This project was developed for educational and professional portfolio demonstration purposes only. All data used is synthetic and does not reflect actual patient records or the infrastructure of a real healthcare entity. It serves to showcase competency in data governance, GRC frameworks, and secure architectural design.*

## Executive Summary
Engineered a "Zero-Trust" data governance framework for a fictional healthcare client (Globalmed) to remediate high-risk PII exposure. This project demonstrates the lifecycle of secure data engineering: from architectural design and RBAC implementation to executive-level risk mitigation.

## The Scenario
Globalmed's internal audits revealed that sensitive patient data was globally accessible to unauthorized internal staff, violating least-privilege principles. The objective was to design a system that obfuscated PII dynamically while maintaining data utility for authorized analytics teams.

## Business Impact & Enterprise Scalability
This architecture was designed to solve a critical business liability while supporting continuous enterprise operations.

**Key Business Outcomes:**
* **Regulatory Audit Readiness:** Mapped technical database controls directly to GDPR and NIST 800-53 requirements. This provides immediate, verifiable evidence of data protection to internal and external auditors, mitigating the risk of regulatory penalties.
* **Zero-Downtime Operations:** Implemented Dynamic Data Masking (DDM) to ensure that downstream data analytics teams maintained uninterrupted access to aggregated datasets. The business continues to function and report accurately without ever exposing raw PII.
* **Internal Threat Mitigation:** Eliminated unauthorized internal access to highly sensitive patient data (SSNs, credit cards) by enforcing strict, role-based least-privilege access at the foundational database tier.

**Enterprise Scalability:**
While this demonstration utilizes localized database roles for proof-of-concept, the architecture is designed for immediate enterprise scaling. In a production environment, these SQL roles map directly to centralized identity management systems (such as Microsoft Entra ID). This allows for the automated, secure provisioning and de-provisioning of access across a massive, multi-departmental corporate structure.

## Key Competencies
* **Data Governance:** Implemented SQL-based Dynamic Data Masking (DDM) to protect sensitive PII without impacting operational utility.
* **Security Engineering:** Built a granular Role-Based Access Control (RBAC) model to enforce least-privilege access.
* **Regulatory Compliance:** Aligned database security architecture with GDPR and NIST 800-53 data protection standards.

## Technical Stack
* **Database:** Azure SQL
* **Security:** Dynamic Data Masking, RBAC, Centralized Identity Readiness

---

## Project Artifact: Data Architecture & Security Validation

To validate the Role-Based Access Control (RBAC) and Dynamic Data Masking (DDM) policies within Azure SQL, the database was queried under three distinct security contexts. Click the buttons below to view the secure database outputs.

**Baseline: Database Owner View**
The system administrator (or DB Owner) retains full visibility into the raw data prior to applying restrictive roles.

[![View Database Owner](https://img.shields.io/badge/View_Database_Owner-0078D4?style=for-the-badge)](https://github.com/beatricekungu/zero-trust-data-governance/blob/main/Assets/Orginal%20screenshot.png)

**Restricted: Data Analyst View**
When querying the database as the `DataAnalyst` role, the masking functions are automatically applied to the Email, SSN, and Credit Card columns. This allows the analyst to perform necessary operational aggregations without exposing raw PII.

[![View Data Analyst](https://img.shields.io/badge/View_Data_Analyst-107C10?style=for-the-badge)](https://github.com/beatricekungu/zero-trust-data-governance/blob/main/Assets/Data%20Analyst%20Screenshot.png)

**Privileged: Chief Compliance Officer (CCO) View**
Because the `ChiefComplianceOfficer` role was granted explicit UNMASK permissions, they retain the ability to view the underlying sensitive data required for audits. 

[![View Chief Compliance Officer](https://img.shields.io/badge/View_Chief_Compliance_Officer-001F3F?style=for-the-badge)](https://github.com/beatricekungu/zero-trust-data-governance/blob/main/Assets/CCO%20Screenshot.png)

---

## Implementation Details & Source Code

The following SQL script demonstrates the core logic used to instantiate the secure data environment, apply dynamic data masking to sensitive PII, and enforce least-privilege access via RBAC.

```sql
-- Globalmed Project: Data Architecture & Security Controls
-- Purpose: Educational/Portfolio Demonstration Only

DROP TABLE IF EXISTS Globalmed_Project;

CREATE TABLE Globalmed_Project (
    PatientID INT IDENTITY(1,1) PRIMARY KEY,
    Region VARCHAR(50) NOT NULL,
    RiskTier VARCHAR(20) NOT NULL,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(150) NOT NULL,
    SSN VARCHAR(15) NOT NULL,
    CreditCard VARCHAR(20) NOT NULL
);

-- Apply Security Controls
ALTER TABLE Globalmed_Project ALTER COLUMN Email ADD MASKED WITH (FUNCTION = 'email()');
ALTER TABLE Globalmed_Project ALTER COLUMN SSN ADD MASKED WITH (FUNCTION = 'partial(0,"XXX-XX-",4)');
ALTER TABLE Globalmed_Project ALTER COLUMN CreditCard ADD MASKED WITH (FUNCTION = 'default()');

-- Define Access Roles
CREATE USER DataAnalyst WITHOUT LOGIN;
CREATE USER ChiefComplianceOfficer WITHOUT LOGIN;

GRANT SELECT ON Globalmed_Project TO DataAnalyst;
GRANT SELECT ON Globalmed_Project TO ChiefComplianceOfficer;
GRANT UNMASK TO ChiefComplianceOfficer;
