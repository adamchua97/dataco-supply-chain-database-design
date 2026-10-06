# DataCo Supply Chain — Data Modeling & Data Warehouse

Relational database design and ER modeling for a global supply chain domain, built as Phase 1 of a graduate Data Modeling & Data Warehouse group project (GBA 6220, Cal Poly Pomona).

> **My contribution (Phase 1):** dataset selection, data preparation, business rules, and ER data model — the foundation the rest of the team built the physical database, data warehouse, and Power BI deliverables on top of.

---

## Overview

- **Domain:** Global supply chain / logistics
- **Source dataset:** [DataCo Smart Supply Chain](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis) (Kaggle) — ~180,000 rows, originally 53 columns
- **Deliverable:** Normalized relational schema — **11 entities**, fully documented with business rules, a data dictionary, and an ERD built in MySQL Workbench

---

## Tech Stack

| Category | Tools |
|---|---|
| Data preparation | Python, Jupyter Notebook, pandas |
| Database design | MySQL, MySQL Workbench (ERD, Chen notation) |
| Documentation | Structured data dictionary, business rules spec |

---

## Why This Dataset

Northwind — the most common academic dataset for this kind of project — was deliberately passed over. It's so widely used in coursework that pre-built ER diagrams and warehouse schemas for it are easy to find online, which risks originality. DataCo's supply chain dataset offered comparable structural richness with far less academic overlap, and its scale (180K rows) meant the data preparation itself had real substance.

---

## Data Preparation

All cleaning was performed in Python (Jupyter Notebook) rather than spreadsheet tools, given the dataset's size.

- Fixed a Latin-encoding issue corrupting special characters in international geographic fields → re-exported as UTF-8-SIG
- Removed placeholder/dummy columns (masked emails, passwords, empty description fields) and columns found to be exact duplicates of others under different names
- Standardized all column names to a consistent SQL-friendly convention
- Restructured a group of columns into a clearly-named line-item (junction) entity
- Standardized a geographic field that mixed Spanish-language values into an English dataset
- Investigated and resolved two ambiguous financial columns by tracing their exact calculation logic against the raw values (e.g., separating a pre-discount gross sales figure from a post-discount net total), removing one column found to be a duplicate and one found to be redundant with an existing profit measure
- **Result:** 53 → 42 columns, no information loss

---

## Schema

### Entities (11 total)

| Type | Entities |
|---|---|
| Core | `CUSTOMER`, `PRODUCT`, `ORDER`, `ORDER_LOCATION` |
| Junction | `LINE` |
| Weak | `SHIPMENT` |
| Lookup | `DEPARTMENT`, `CATEGORY`, `SHIPPING_MODE`, `PAYMENT_TYPE`, `MARKET` |

### Relationships

| From | Relationship | To | Cardinality |
|---|---|---|---|
| CUSTOMER | places | ORDER | 1:M |
| ORDER | contains | LINE | 1:M |
| PRODUCT | is included in | LINE | 1:M |
| DEPARTMENT | manages | CATEGORY | 1:M |
| CATEGORY | classifies | PRODUCT | 1:M |
| ORDER | generates | SHIPMENT | 1:1 |
| SHIPPING_MODE | defines | ORDER | 1:M |
| PAYMENT_TYPE | processes | ORDER | 1:M |
| MARKET | encompasses | ORDER_LOCATION | 1:M |
| ORDER_LOCATION | receives | ORDER | 1:M |

### Notable design decisions

- **Surrogate keys** (`AUTO_INCREMENT`) introduced for 5 entities with no natural key in the source data: `SHIPMENT`, `SHIPPING_MODE`, `PAYMENT_TYPE`, `MARKET`, `ORDER_LOCATION`
- **1:1 enforcement** — `SHIPMENT.ORDER_ID` carries a `UNIQUE` constraint (not just documentation) to guarantee one shipment per order
- **Derived-value elimination** — two ambiguous/duplicate financial columns were removed from the physical model to avoid conflicting sources of truth
- **Data types** — `DECIMAL(10,2)` for monetary fields, `DECIMAL(5,4)` for ratio fields, `VARCHAR(10)` for zip codes (preserves leading zeros), `TINYINT(1)` for binary flags, `DATETIME` for all date fields

---

## Documentation

- [`DATACO-PROJECT-BUSINESS-RULES.pdf`](DATACO-PROJECT-BUSINESS-RULES.pdf) — entity integrity, referential integrity, cardinality, valid value sets, nullable field justification; also available as [Word](GBA6220-GROUP2-PROJECT-BUSINESS-RULES.docx)
- [`DATACO-PROJECT-DATA-DICTIONARY.pdf`](DATACO-PROJECT-DATA-DICTIONARY.pdf) — full column-level definitions, data types, constraints; also available as [Word](GBA6220-GROUP2-PROJECT-DATA-DICTIONARY.docx)
- [`dataco-final-project-erd.mwb`](dataco-final-project-erd.mwb) — MySQL Workbench ER diagram (Chen notation); also available as [PDF](dataco-final-project-erd.pdf) and [PNG](dataco-final-project-erd.png)
- [`DATA-PREPARATION-PROCESS.docx`](DATA-PREPARATION-PROCESS.docx) and [`dataco-data-preprocess.ipynb`](dataco-data-preprocess.ipynb) — data preparation write-up and cleaning notebook
- [`portfolio_case_study.docx`](portfolio_case_study.docx) — portfolio case study

---

## Project Context

Built as part of a 4-person team project. Grading breakdown: data model (20%), database (35%), data warehouse (35%), presentation (10%). This repository/documentation covers the Phase 1 portion of the project — the ER data model and business rules underlying the rest of the team's database, data warehouse, and BI work.
