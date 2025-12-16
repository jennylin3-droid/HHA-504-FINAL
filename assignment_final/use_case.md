## Use Case: Medication Safety Check Using Drug Lab Interaction Rules
Problem Statement
Medication related adverse events remain a patient safety concern in healthcare, particularly when prescribed medications conflict with abnormal laboratory values. Certain drugs require close monitoring of specific labs and failure to recognize unsafe combinations.
Example: Prescribing potassium-sparing medications in the setting of hyperkalemia.
It can lead to serious complications. In busy clinical environments, these checks are often performed manually or inconsistently, increasing the risk of oversight.

This project proposes a cloud based medication safety checking system that evaluates whether a patient’s laboratory value is compatible with a prescribed medication from a metric based clinical thresholds. The system is designed as a decision-support aid rather than a diagnostic tool, helping clinicians quickly identify potential safety warnings before medication continuation or initiation.

*Primary users*
- Clinicians like physicians, physician assistants, and nurse practitioners
- Pharmacists and clinical support staff

Data Sources

- This system relies on structured, non-identifiable data inputs that simulate clinical workflows:
1. *Medication and laboratory input (JSON)*
   - Source: User input from clinical decision-support request dashboard
   Stored as:
Raw JSON files in cloud object storage for traceability
Structured records in a managed SQL database

- Example fields:
     - `drug` (string, e.g., "spironolactone")
     - `lab_name` (string, e.g., "potassium")
     - `lab_value` (numeric, e.g., 5.5)
     - `unit` (string, e.g., "mmol/L")


2. *Medication lab rule definitions (CSV)*
   - Source: published clinical references (e.g., drug monographs, guideline summaries)
   - Example rule:
     - Potassium-sparing diuretics are unsafe when serum potassium > 5.0 mmol/L


Basic Workflow
1. User enters a medication name and lab value in a dashboard form (or submits JSON to an API endpoint).
2. The request is sent to an HTTP serverless function for validation and rule evaluation.
3. The function returns a JSON response (`safe` vs. `warning`) with an explanation.
4. The system stores the raw request and response JSON in cloud object storage for traceability.
5. A processing step writes structured results (drug, lab value, status, timestamp) into a managed SQL database.
6. A scheduled job computes daily summaries (e.g., number of warnings by drug/lab) and saves summary tables.
7. The dashboard queries the SQL database and displays recent warnings and summary metrics.


