flowchart LR
  %% USERS
  U["Clinicians / PAs / NPs\nPharmacists / Clinical Staff"] -->|Enter med + lab| FE

  %% FRONTEND / ACCESS LAYER
  subgraph Access["Frontend / Access Layer"]
    FE["Medication Safety Check Dashboard\n(Web Form)"]
    API["API Endpoint\nPOST /check"]
    FE --> API
  end

  %% COMPUTE LAYER
  subgraph Compute["Compute Layer"]
    SF["HTTP Serverless Function\nValidate JSON + Apply Drug–Lab Rule\nReturn: safe / warning + reason"]
    SCH["Scheduled Job (Serverless / Scheduler)\nDaily Summaries"]
  end

  %% DATA LAYER
  subgraph Data["Data Layer"]
    OS["Cloud Object Storage\nRaw request/response JSON\n+ Rules CSV (versioned)"]
    DB["Managed SQL Database\nresults + flags + summaries"]
  end

  %% FLOW
  API -->|JSON request:\n drug, lab_name, lab_value, unit| SF
  SF -->|JSON response:\n status + reason| API

  %% TRACEABILITY + PERSISTENCE
  SF -->|Store raw request/response| OS
  OS -->|Rules CSV read| SF

  SF -->|Write structured result\n(drug, lab, value, status, timestamp)| DB
  SCH -->|Compute daily summaries| DB

  FE -->|Query warnings + summaries| DB

  NB -->|Query| DB
  NB -->|Write reports (CSV)| OS
