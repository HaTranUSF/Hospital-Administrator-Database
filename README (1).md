# Hospital Administrator Database Design Project

A comprehensive relational database design documentation (DBDD) for a hospital administration system to optimize ward management, patient admissions, nurse schedules, clinical treatments, and financial charting. 

## 📋 Project Overview
This project establishes a robust relational database schema tailored for hospital administrators. It provides full data tracking capabilities for:
- **Staffing & Management:** Nurse ward assignments, rotation schedules, and hierarchical supervisor networks.
- **Clinical Operations:** Patient admissions/discharges linked to doctors, physical bed tracking, and multi-disciplinary treatment administration.
- **Financial & Logistics:** Granular logging of consumable items and medical procedures charged to patients during their clinical stay.
- **Patient Care Analytics:** Granular event logging (wellness checks, medication runs, assistance, etc.) to capture fine-grained nurse-patient care interactions.

---

## 🏗️ System Architecture & Schema Design

### 1. Conceptual & Logical Models
The architecture is structured across standard clinical and operational entities mapped via Entity-Relationship Diagrams (ERD) and relational normalization guidelines:
- **Nurse & Ward Subsystem:** Implements recursive relationships for management (`SupervisorNo` references `NurseID`) and multi-to-multi junction mapping (`NurseAssignment`) for rotational staffing.
- **Bed & Patient Tracking:** Ensures atomic transactional control over bed inventory allocations, availability status codes (`O` for Occupied, `A` for Available), and dimensional rules (S, M, L, XL sizes).
- **Admissions & Clinical Care:** Explicit separate mapping for operational admissions (`AdmitDischarge` tracking separate admitting/discharging physicians) and active medical interactions (`TreatmentAdministration`).

### 2. Comprehensive Data Dictionary
The database schema utilizes rigorous constraints, custom data validation rules, and structural indexes across 13 core relational tables:

| Table Name | Primary / Foreign Keys | Critical Data Validations & Check Constraints |
| :--- | :--- | :--- |
| **Certification** | `CertID` (PK) | Auto-incrementing identities, unique certification tags. |
| **NurseCert** | `NurseID` (CPK, FK), `CertID` (CPK, FK) | Composite key bridging nurses to active specialties. |
| **Nurse** | `NurseID` (PK) | Recursive FK (`SupervisorNo`), ISO State constraint (`LIKE '[A-Z][A-Z]'`), Zip pattern verification (`LIKE '[0-9]'`x5). |
| **NurseAssignment**| `AssignmentID` (PK), `NurseID`, `WardID` (FKs) | Clocked hour decimals (`decimal(4,2)`), transactional work-date logging. |
| **Ward** | `WardID` (PK), `ChargeNurse` (FK) | Unique department tags, 1:1 custodian validation rule for Charge Nurses. |
| **Bed** | `BedID` (PK), `WardID` (FK) | Status checks: Size (`S`, `M`, `L`, `XL`), Type (`E`lectric, `M`anual), Availability (`O`, `A`). Default sets to Large/Manual. |
| **Patient** | `PatientID` (PK), `BedID` (FK) | Computed age mapping (`datediff(year, DOB, getdate())`), strict gender domain tracking (`M`, `F`, `NA`). |
| **ItemCharge** | `ChargeID` (PK), `PatientID`, `ItemID` (FKs) | Temporal tracking of volume metrics (`Quantity`) for supply log analysis. |
| **Item** | `ItemID` (PK) | Financial mapping utilizing precise currency formats (`money`). |
| **AdmitDischarge** | `AdmitID` (PK), `PatientID`, `AdmitDoc`, `DischargeDoc` | Historical temporal boundaries monitoring distinct admission and discharge doctors. |
| **Physician** | `DoctorID` (PK) | Comprehensive contact vectors, standardized state code constraints. |
| **PhysicianSpecialty**| `DoctorID` (CPK, FK), `SpecialtyID` (CPK, FK) | N:M mapping separating a clinical provider across multiple operational lines. |
| **Specialty** | `SpecialtyID` (PK) | Unique academic and institutional healthcare specialty references. |
| **TreatmentAdmin** | `PatientID`, `DoctorID`, `TreatmentID`, `DateTime` (CPK)| High-throughput operational transactional tracking capturing chronological results. |
| **Treatment** | `TreatmentID` (PK) | Base service menu structuring flat financial cost guidelines (`money`). |
| **NursePatient** | `EventID` (PK), `PatientID`, `NurseID` (FKs) | Categorical domain check: `wellness check`, `medication`, `food service`, `assistance`, `treatment admin`, `other`. |

---

## 🛠️ Tech Stack & Implementation Directives
- **DBMS Environment:** Microsoft SQL Server / Azure SQL Database / PostgreSQL
- **Modeling Paradigms:** Relational Schema (RS), Enhanced Entity-Relationship Diagrams (EERD)
- **Data Types Standardized:** `int (Identity)`, `nvarchar(X)`, `char(X)`, `date`, `datetime`, `time`, `money`, `computed fields`

---

## 🚀 Portfolio Highlights
- **Hierarchical Self-Referential Integrity:** Showcases advanced data modeling utilizing recursive foreign keys (`Nurse.SupervisorNo`) to build organization hierarchies without schema bloat.
- **Domain Constraint Precision:** Implements aggressive string verification regex rules (`LIKE` filters) directly inside the database tier to maximize system data sanitation.
- **Complex Financial Line-Item Auditing:** decouples logistics (`ItemCharge`, `TreatmentAdministration`) away from customer core tables to protect analytical audit trails.

---
*Document author: Ha Tran | Portfolio Showcase Asset*
