## High-level Architecture Diagram (Multi-Cloud)

```mermaid
flowchart LR
  %% USERS
  U[Clinician / Pharmacist / Student] -->|Web UI| FE

  %% FRONTEND / ACCESS LAYER
  subgraph Access["Frontend / Access Layer"]
    FE["Flask Dashboard + API (Container)\n(Azure Container Apps OR GCP Cloud Run)"]
  end

  %% COMPUTE LAYER - SERVERLESS TRIAGE
  subgraph Compute["Compute Layer"]
    AF["Azure Functions (HTTP Trigger)\nMedication–Lab Safety Check"]
    GF["GCP Cloud Functions 2nd Gen / Cloud Run Functions (HTTP Trigger)\nMedication–Lab Safety Check"]
    ETL["Event-Driven ETL\n(Azure Function Blob Trigger OR GCP Eventarc Function)"]
    SCH["Scheduled Summary Job\n(Azure Timer Trigger OR Cloud Scheduler + Function)"]
  end

  %% DATA LAYER
  subgraph Data["Data Layer"]
    AS["Object Storage\n(Azure Blob Storage OR GCP Cloud Storage)\nRaw Requests/Responses + Rules CSV/JSON"]
    SQL["Managed SQL Database\n(Azure SQL OR Cloud SQL)\nresults + flags + summaries"]
  end

  %% OPTIONAL ANALYTICS/AI
  subgraph Analytics["Optional Analytics / AI"]
    NB["Notebook / Analytics\n(Azure ML Notebook OR Vertex AI Workbench)\nTrend summaries + reports"]
  end

  %% FLOWS
  FE -->|Call triage endpoint| AF
  FE -->|Call triage endpoint| GF

  AF -->|Return JSON status| FE
  GF -->|Return JSON status| FE

  FE -->|Write raw request/response| AS
  AS -->|New object triggers| ETL
  ETL -->|Insert structured rows| SQL

  SCH -->|Daily aggregation| SQL
  NB -->|Query + analysis| SQL
  NB -->|Write reports (CSV)| AS

  %% MONITORING (optional label)
  FE -. logs/metrics .-> M[(Monitoring\nApp Insights / Cloud Logging)]
  AF -. logs/metrics .-> M
  GF -. logs/metrics .-> M
  ETL -. logs/metrics .-> M
  SCH -. logs/metrics .-> M
