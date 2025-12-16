# Medication Safety Check – Cloud Integration Mini-Capstone
## Project Overview
This project implements a cloud-based medication safety checking system that evaluates whether a laboratory value is compatible with a prescribed medication using simple, rule-based clinical thresholds. The system is designed as a decision-support aid to help identify potentially unsafe drug–laboratory value combinations before medication continuation or initiation.

The solution integrates multiple cloud services, including serverless compute, Ubuntu-based application hosting, cloud storage, and managed SQL databases. The architecture builds directly on prior coursework in cloud fundamentals, serverless functions, Flask applications, SQL databases, and inferential analytics.

## Healthcare Use Case
Medication-related adverse events can occur when abnormal laboratory values are overlooked during prescribing. This project focuses on detecting unsafe drug–lab interactions, such as potassium-sparing medications in the setting of elevated serum potassium. The system provides a structured warning response rather than a diagnosis and is intended for educational and prototype purposes only.

**Primary users**
- Clinicians (physicians, physician assistants, nurse practitioners)
- Pharmacists and clinical support staff

## Architecture Summary
The system consists of the following components:
- Frontend / Access Layer: Flask web dashboard running on Ubuntu-based compute
- Compute Layer:HTTP-triggered serverless functions for rule evaluation
- Data Layer: Cloud object storage for raw JSON and rules, managed SQL database for structured results
- Analytics:Scheduled serverless job for daily summary statistics

A detailed architecture diagram and implementation plan are provided in `architecture_plan.md`.

---

## Cloud Services Used
- Compute:Azure Functions / GCP Cloud Functions (serverless), Ubuntu-based Flask app
- Storage:Azure Blob Storage / GCP Cloud Storage
- Database:Azure SQL Database / Cloud SQL
- Analytics:Scheduled serverless jobs
- Monitoring:Cloud logging and monitoring tools

---

## Relation to Course Assignments
This project integrates concepts from multiple assignments:
- Assignment 1 – Intro to Cloud (GCP and OCI):Object storage and cloud service fundamentals
- Assignment 2 – VMs and Networking with Flask:Ubuntu-based Flask application and networking concepts
- Assignment 2 – SQL DB HHA 507:Managed SQL database design and querying
- AHI 504 – Multi-Cloud Serverless Function Assignment:Serverless logic and JSON validation
- Inferential Assignment:Aggregate metrics and summary analytics