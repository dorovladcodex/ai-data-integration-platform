# SAP BW file-based integration

The upstream SAP BW team delivers files to a shared FTP server. Each interface has its own folder, usually for one country; a special multi-country interface may be mapped differently.

## File delivery

Typical interface files:

| File | Meaning |
|---|---|
| START flag | File transfer has started |
| Schema file | Source fields and types |
| CSV file | Extracted data |
| END flag | Completion state, e.g. Complete / Error |

The receiving pipeline should not treat a partially delivered file as ready. For production use, it should verify the relevant completion indicator, expected files, batch identity and duplicate-delivery handling.

## Provisioning

```mermaid
flowchart TD
    A["Jira: SAP BW interface"] --> B["SAP BW SKILL.md"]
    B --> C["GitLab branch from dev"]
    C --> D["Databricks FTP utility: fetch schema"]
    D --> E["Update interface parameters"]
    E --> F["Databricks executes Python generator"]
    F --> G["RAW schema"]
    F --> H["STAGE schema"]
    F --> I["RAW → STAGE SQL"]
    G --> V["Validate generated artifacts"]
    H --> V
    I --> V
    V --> K["Commit to GitLab feature branch"]
    K --> L["Prepare ADF definition in Azure DevOps"]
    L --> M["GitLab MR + Azure DevOps PR"]
    M --> N["Human approval and deployment"]
```

The Python generator replaces repetitive manual edits once performed from VS Code. In the reference design, artifacts are validated and committed through a controlled Git adapter. This avoids assuming that a Databricks Git folder automatically pushes newly created files.

## Runtime

```mermaid
flowchart LR
    FTP["SAP BW FTP delivery"] --> ADF["One ADF pipeline"]
    ADF --> N1["1. Transfer to internal storage"]
    N1 --> N2["2. Load file-based RAW"]
    N2 --> N3["3. Transform to Delta STAGE"]
    N3 --> LAKE[("Data Lake")]
```

The ADF pipeline invokes three Databricks processing steps and includes error monitoring. The generated transformation adds technical fields and country-aware partitioning. Several country-specific interfaces can feed one target table. A multi-country marker must not automatically overwrite actual country values in the data.

## Repository split

GitLab tracks interface settings, source schema and generated SQL. Azure DevOps tracks ADF pipeline definitions and their review/deployment workflow. A shared change-set ID links both reviews, but their merges are separate operations.
