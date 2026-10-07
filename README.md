# Subsystem 1: Project & Results Management — Design Documentation (v3.0)

## Subsystem 1 Collaboration Diagram (CS4311 Wirfs-Brock Standard)

```mermaid
flowchart LR
    %% Class Styles
    classDef ext fill:#f1f3f5,stroke:#495057,stroke-width:2px,color:#212529,rx:14,ry:14;
    classDef s1class fill:#ffffff,stroke:#1971c2,stroke-width:2px,color:#1864ab,rx:8,ry:8;
    classDef s1store fill:#e7f5ff,stroke:#0c8599,stroke-width:2px,color:#0b7285,rx:8,ry:8;

    %% External Subsystem Boundaries (Team 2 & Team 12)
    S3_API(["Subsystem 3: ProjectsRouter REST"]):::ext
    S3_Holder(["Subsystem 3: LLM Prompt-Response Holder"]):::ext
    S2_Tool(["Subsystem 2: Exploitation Tools Prompter"]):::ext
    S2_Report(["Subsystem 2: ReportManager"]):::ext

    %% All 9 Subsystem 1 Internal Classes
    PM["Project Manager (C6)<br><b>Contracts 8, 9</b>"]:::s1class
    PIH["Project Information Holder (C5)<br><b>Contract 7</b>"]:::s1class
    PRepo["Project Repository (C7)<br><b>Contracts 10, 11</b>"]:::s1store
    DBSM["Database Session Manager (C2)<br><b>Contracts 3, 4</b>"]:::s1store
    PyRIT["PyRIT Interfacer (C8)<br><b>Contracts 12, 13</b>"]:::s1class
    Attack["Attack (C1)<br><b>Contracts 1, 2</b>"]:::s1class
    Metrics["Metrics Calculator (C3)<br><b>Contracts 5, 6</b>"]:::s1class
    RRepo["Results Repository<br><b>Contracts 14, 15, 16</b>"]:::s1store
    RView["Results View<br><b>Contract 17</b>"]:::s1class

    %% 1. Project Management Flow
    S3_API -->|Contract 8| PM
    PM -->|Contract 7| PIH
    PM -->|Contract 10, 11| PRepo
    PRepo -->|Contract 7| PIH
    PRepo -->|Contract 3| DBSM

    %% 2. Execution & Persistence Flow
    S2_Tool -->|Contract 12, 13| PyRIT
    PyRIT -->|Contract 1, 2| Attack
    PyRIT -->|Contract 14| RRepo
    RRepo -->|Contract 1, 2| Attack
    RRepo -->|Contract 3| DBSM
    S3_Holder -->|Contract 14| RRepo

    %% 3. Analytics & Team 12 Handoffs
    Metrics -->|Contract 1| Attack
    S3_Holder -->|Contract 5| Metrics
    Metrics -->|Contract 6: Metric Provision| S2_Report
    RRepo -->|Contract 16: Results Provision| S2_Report

    %% 4. Results View Presentation Flow
    RView -->|Contract 1, 2| Attack
    RView -->|Contract 6| Metrics
    RView -->|Contract 7| PIH
    RView -->|Contract 15| RRepo
    S3_Holder -->|Contract 17| RView
```

---

## Subsystem 1 (Version 3.0) Contract Mapping Table

| Class Name | Global Contracts | Primary Role & Responsibilities | Collaborating Entities |
| :--- | :--- | :--- | :--- |
| **Attack (C1)** | Contract 1, 2 | Attack state, timestamps, and execution progress tracking | Results View, PyRIT Interfacer, Results Repository, Metrics Calculator |
| **Database Session Manager (C2)** | Contract 3, 4 | PostgreSQL connection pool and health checks | Project Repository, Results Repository |
| **Metrics Calculator (C3)** | Contract 5, 6 | Calculates pass/fail, attack ratios, and severity distributions | Attack, Subsystem 3 (Prompt Holder), Results View, Subsystem 2 (ReportManager) |
| **Project Information Holder (C5)** | Contract 7 | Project entity metadata container (id, name, dates, analyst) | Project Manager, Project Repository, Results View |
| **Project Manager (C6)** | Contract 8, 9 | Project creation, open, delete, and XML import lifecycle | Project Repository, Project Info Holder, Subsystem 3 (ProjectsRouter) |
| **Project Repository (C7)** | Contract 10, 11 | PostgreSQL CRUD operations for project records | Database Session Manager, Project Manager, Project Info Holder |
| **PyRIT Interfacer (C8)** | Contract 12, 13 | PyRIT flag configuration and offline assessment execution | Attack, Results Repository, Subsystem 2 (Exploitation Tools Prompter) |
| **Results Repository** | Contract 14, 15, 16 | PostgreSQL persistence for attack records, prompts, and status | Database Session Manager, Attack, PyRIT, Results View, Subsystems 2 & 3 |
| **Results View** | Contract 17 | Displays project summary metrics, attack tables, and visualizations | Attack, Metrics Calculator, Project Info Holder, Results Repo, Subsystem 3 |
