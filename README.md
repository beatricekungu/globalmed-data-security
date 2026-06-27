# Globalmed Project: Privacy & Compliance Architecture

> **Disclaimer:** This project was developed for educational and professional portfolio demonstration purposes only. All data used is synthetic and does not reflect actual patient records or the infrastructure of a real healthcare entity. It serves to showcase competency in data governance, GRC frameworks, and secure architectural design.

## Executive Summary
Engineered a "Zero-Trust" data governance framework for a fictional healthcare client (Globalmed) to remediate high-risk PII exposure. This project demonstrates the lifecycle of secure data engineering: from architectural design and RBAC implementation to executive-level risk reporting.

## The Scenario
Globalmed's internal audits revealed that sensitive patient data was globally accessible to unauthorized internal staff, violating least-privilege principles. The objective was to design a system that obfuscated PII dynamically while maintaining data utility for authorized analytics teams.

## Key Competencies
* **Data Governance:** Implemented SQL-based Dynamic Data Masking (DDM) to protect sensitive PII without impacting operational utility.
* **Security Engineering:** Built a granular Role-Based Access Control (RBAC) model to enforce least-privilege access.
* **Executive Reporting:** Visualized risk exposure and compliance posture for C-suite stakeholders via an integrated Power BI compliance dashboard.

## Technical Stack
* **Database:** Azure SQL
* **Security:** Dynamic Data Masking, RBAC, NIST 800-53 Mapping
* **Visualization:** Power BI (Compliance/Risk Monitoring)

---

## Project Artifacts

### 1. Data Architecture & Security
The core infrastructure was built to ensure PII (SSN, Email, Credit Card) remained obfuscated from unauthorized internal roles.

<img width="1659" height="727" alt="Orginal screenshot" src="https://github.com/user-attachments/assets/90e406eb-0fd2-494d-8277-77dbb5c6fede" />

<img width="1658" height="806" alt="Data Analyst Screenshot" src="https://github.com/user-attachments/assets/3d1e766d-dc92-4775-85d1-5cca1ad378b3" />

<img width="1656" height="766" alt="CCO Screenshot" src="https://github.com/user-attachments/assets/4fc3d724-a83a-4664-ae1e-466d2724365a" />


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
