# Spark Runtime Deprecation Tracker

A personal project I built to understand how Azure service deprecations are managed at scale — specifically the Spark runtime retirement cycle in Microsoft Fabric.

The idea came from reading through Microsoft's Azure Retirement Policy and wanting to simulate what the actual operational workflow looks like: which tenants are affected, who hasn't responded, and how you'd prioritise outreach before the disablement date.

---

## What this project covers

- A simulated dataset of 30 tenants across multiple regions, with varying runtime versions, migration statuses, and acknowledgement states
- KQL queries written against that dataset to answer the key operational questions
- A Power BI dashboard for tracking migration progress and escalation priorities at a glance

It doesn't cover any actual pipeline work or ADF — just the monitoring and outreach side, which is what I found most interesting from a customer operations perspective.

---

## Repository Structure

```
├── tenant_deprecation_data.csv       # Simulated tenant dataset (30 tenants)
├── kql/
│   ├── 01_deprecated_tenants.kql             # All tenants on deprecated runtimes
│   ├── 02_unacknowledged_escalation.kql      # Tenants who haven't acknowledged yet
│   ├── 03_migration_progress_by_runtime.kql  # Migration status by runtime version
│   └── 04_escalation_urgency_ranking.kql     # Urgency ranking for outreach prioritisation
├── dashboard/
│   └── screenshots/                          # Power BI dashboard screenshots
└── README.md
```

---

## Dataset

**File:** `tenant_deprecation_data.csv`

30 simulated tenants. Spark 2.4 and Spark 3.1 are the deprecated versions. Spark 3.3 is active. A few fields are intentionally left blank (e.g. LastContactDate) to reflect realistic data gaps you'd see in a live tracking sheet.

| Column | Description |
|---|---|
| TenantID | Unique identifier |
| TenantName | Organisation name |
| Region | Geographic region |
| RuntimeVersion | Current Spark runtime in use |
| DeprecationStatus | Deprecated or Active |
| AcknowledgementStatus | Whether the tenant has acknowledged the notice |
| MigrationStatus | Not Started / In Progress / Completed / Blocked |
| DaysToDisablement | Days until the runtime is disabled (0 = past deadline) |
| LastContactDate | Last outreach attempt date |
| AssignedCSM | Customer Success Manager for this tenant |
| EscalationTier | Tier 1 (low risk) to Tier 3 (not responded) |

---

## KQL Queries

Written for Azure Data Explorer. To run these, ingest the CSV as a table called `tenant_deprecation_data` and run the queries from the `kql/` folder.

**Query 1** — Base list of all deprecated tenants, sorted by days to disablement. Starting point for any outreach planning.

**Query 2** — Filters for tenants who haven't acknowledged the deprecation notice. These are Tier 3 and need immediate follow-up.

**Query 3** — Migration progress summarised by runtime version, including a migration rate % column. Useful for weekly reporting.

**Query 4** — Urgency ranking. Assigns a score (1 = most critical) based on days remaining and acknowledgement status. Guides the order of outreach calls.

---

## Power BI Dashboard

Built in Power BI Desktop using the CSV as the data source.

KPI cards: total deprecated tenants, migration rate %, tenants not acknowledged, average days to disablement.

Visuals: migration status by runtime version, acknowledgement breakdown, Tier 3 escalation list, tenant distribution by region.

Screenshots in `dashboard/screenshots/`.

---

## How to Use

1. Download `tenant_deprecation_data.csv`
2. Open Azure Data Explorer (aka.ms/lademo for a free demo environment) and ingest the CSV as a table named `tenant_deprecation_data`
3. Run the `.kql` files from the `kql/` folder against the ingested table
4. Open `Spark_Deprecation_Tracker.pbix` in Power BI Desktop to explore the dashboard

---

## Notes

This is a simulation — the tenant names and data are made up. The deprecation lifecycle structure is modelled on Microsoft's actual Azure Retirement Policy documentation. The playbook document (`Spark_Deprecation_Playbook.docx`) has more detail on the communication framework and escalation tiers I designed alongside this.
