# Dundas BI → Power BI Migration

## Solution Architecture

Three-layer hybrid orchestration:
- **SYNC layer** (Azure VM): SQL Agent runs sync-queue every 10s → real staging tables
- **COPY layer** (Azure Functions): Timer trigger copies deltas every 10s → Azure SQL DB
- **BATCH layer** (ADF): Nightly SCD merge → Warehouse star schema → Power BI refresh

## Project Structure

```
DundasMigration/
├── src/
│   ├── Functions/StagingCopy/     # Azure Functions (C# .NET 8) — EPIC-9
│   ├── Database/Warehouse/        # SQL Database project (dacpac) — EPIC-4
│   ├── Database/StagingRefactor/  # VM-side staging scripts — EPIC-9
│   ├── Database/NewETLProcs/      # New ETL stored procedures — EPIC-3
│   ├── ADF/                       # ADF ARM templates — EPIC-5
│   ├── Infrastructure/            # Bicep IaC templates — EPIC-10
│   └── PowerBI/                   # Power BI projects (.pbip) — EPIC-6
├── tests/
│   ├── StagingCopy.Tests/         # Function unit + integration tests
│   └── Database.Tests/            # SQL merge + reconciliation tests
├── pipelines/                     # Azure DevOps CI/CD YAML — EPIC-10
├── docs/                          # All deliverable documents + ADRs
├── scripts/
│   ├── discovery/                 # ETL discovery scripts — EPIC-1, EPIC-2
│   ├── benchmark/                 # PoC benchmark scripts
│   └── poc/                       # PoC setup and simulation
└── DundasMigration.sln
```

## Prerequisites

- Visual Studio 2022 (17.8+)
- .NET 8 SDK
- Azure Functions Core Tools v4
- SQL Server Data Tools (SSDT)
- Azure CLI
- Bicep CLI

## Getting Started

1. Clone the repo
2. Open `DundasMigration.sln` in Visual Studio 2022
3. Update `src/Functions/StagingCopy/local.settings.json` with your connection strings
4. Build solution (Ctrl+Shift+B)
5. Run PoC scripts in `scripts/poc/` against your DEV SQL Server
6. F5 to run the Azure Function locally

## Epic → Project Mapping

| Epic | Project/Folder | Description |
|------|---------------|-------------|
| EPIC-1 | scripts/discovery/ | ETL discovery scripts |
| EPIC-2 | scripts/discovery/ | Fact table source mapping |
| EPIC-3 | src/Database/NewETLProcs/ | New ETL stored procedures |
| EPIC-4 | src/Database/Warehouse/ | Star schema DDL + merge procs |
| EPIC-5 | src/ADF/ | ADF pipeline ARM templates |
| EPIC-6 | src/PowerBI/ | Power BI semantic models |
| EPIC-7 | tests/Database.Tests/ | Reconciliation test scripts |
| EPIC-8 | src/Infrastructure/ | Key Vault + Entra ID config |
| EPIC-9 | src/Functions/ + src/Database/StagingRefactor/ | Staging copy + refactoring |
| EPIC-10 | pipelines/ + src/Infrastructure/ | CI/CD + IaC |
