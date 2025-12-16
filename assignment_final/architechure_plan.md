# Architecture Plan: Medication Safety Check

#### Overview
This mini-capstone designs a cloud-based medication safety checking system that evaluates whether a laboratory value is compatible with a prescribed medication using metric-based clinical thresholds. Medication related adverse events often occur when abnormal laboratory values are overlooked during prescribing or medication continuation. This system functions as a *clinical decision support aid*, helping clinicians and pharmacists quickly identify potential safety warnings before patient harm occurs.

The architecture integrates *Ubuntu-based compute*, *serverless functions*, *cloud object storage*, *managed SQL databases*, and *scheduled analytics*. Ubuntu Linux is intentionally selected across compute components to minimize licensing costs while maintaining compatibility with Python, Flask, and cloud-native tooling. The design directly builds on prior coursework, particularly the *multi cloud serverless assignment** and *Flask + SQL database assignments*, by extending those components into an end-to-end cloud solution.

---

## Service Mapping (Cloud Services + Course Alignment)

| Layer | Service (Cloud) | Role in Solution | Related Assignment / Module |
|------|------------------|------------------|-----------------------------|
| Frontend / Access | **Flask App on Ubuntu-based compute** (Azure Container Apps or Ubuntu Azure VM / GCP Cloud Run) | Web dashboard and API for submitting medication and lab values; Ubuntu chosen to reduce OS licensing and compute costs | **Assignment 2: VMs and Networking with Flask** |
| Compute (Serverless) | **Azure Functions (HTTP trigger)** / **GCP Cloud Functions 2nd Gen** | Core medication–lab safety logic: validate JSON, apply rule thresholds, return structured response | **AHI 504 – Multi-Cloud Serverless Function Assignment** |
| Storage | **Azure Blob Storage** / **GCP Cloud Storage** | Store raw request/response JSON, medication–lab rule CSV files, and exported reports | **Assignment 1 – Intro to Cloud (GCP and OCI)** |
| Database / SQL | **Azure SQL Database** / **Cloud SQL** | Persist structured safety check results and summary tables for reporting | **Assignment 2 – SQL DB 507** |
| Analytics / Reporting | **Scheduled Serverless Job** (Azure Timer Trigger / Cloud Scheduler + Function) | Compute daily aggregates and trends from stored results | **Inferential Assignment** |
| Monitoring | Azure Monitor + Application Insights / Cloud Logging | Logging, error tracking, and performance monitoring | Reinforced across serverless and cloud modules |

## Data Flow Narrative

**Step 1:**  
A clinician or pharmacist enters a medication name and laboratory value through a Flask-based dashboard running on Ubuntu-based compute, or submits a JSON request via an API endpoint.

**Step 2:**  
The request is forwarded to an HTTP triggered serverless function (Azure Functions or GCP Cloud Functions), which validates required fields and data types.

**Step 3:**  
The serverless function evaluates the medication lab interaction using predefined clinical threshold rules and returns a structured JSON response indicating *safe* or *warning*, along with an explanation.

**Step 4:**  
The system stores the raw request and response JSON in cloud object storage for traceability and auditing. Rule definition CSV files are also stored and versioned in object storage.

**Step 5:**  
Structured results are written to the `safety_check_results` table in the managed SQL database.

**Step 6:**  
A scheduled serverless job runs daily to compute aggregate metrics such as warning counts by medication and laboratory test. These results are written to the `daily_summary` table.

**Step 7:**  
The Flask dashboard queries the SQL database to display recent warnings and summary metrics to users.

## Security, Identity, and Governance Basics
Credentials are managed using environment variables and cloud-native secret management services (Azure Key Vault or GCP Secret Manager) rather than being embedded in application code. Ubuntu-based compute instances and serverless functions use least-privilege identities to access only required resources such as storage containers and SQL databases. Role-based access control ensures that users can view safety results while administrative functions (such as modifying rules) are restricted.

To avoid exposing protected health information (PHI), the system uses **de-identified data only**. No patient identifiers such as names, dates of birth, addresses, or medical record numbers are included. In a production environment, additional controls such as private networking, encryption, audit logs, and compliance reviews would be required.

## Cost and Operational Considerations
The primary cost drivers in this architecture are managed SQL database usage and frontend compute resources. The most costly is Compute, because it is billed based on time and resource allocation, not just usage volume. In healthcare applications with dashboards or APIs that must remain available, meaning the costs racks up. Virtual machines or containerized applications consume CPU and memory continuously while they are running, even during periods of low activity. To minimize expenses, the system uses **Ubuntu** across the compute server, eliminating operating system licensing costs while maintaining stability and compatibility with cloud healthcare applications. Serverless functions are used for both real time medication safety checks and scheduled analytics, ensuring that compute resources are only consumed when needed.

Object storage costs remain low due to the small size of JSON and CSV files. The SQL database can be deployed on a basic tier suitable for a student or prototype environment. Scheduled analytics jobs can run once daily to further reduce compute usage. Monitoring and logging are enabled to support troubleshooting and performance review without additional infrastructure overhead.
