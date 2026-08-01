# Schema Change Impact Report — `raw.orders`

> `order_total` renamed to `order_amount`

| | |
|---|---|
| **Dataset** | `raw.orders` |
| **Classification** | `breaking` |
| **Risk** | 🔴 Critical |
| **Downstream assets affected** | 8 |
| **Consumers needing updates** | 2 |
| **Detected** | 2026-08-01 17:40 UTC+0530 |

*Generated automatically by DataHub Autopilot from DataHub lineage, ownership and schema metadata. Every figure below is computed, not estimated.*

## What changed

The 'order_total' column has been renamed to 'order_amount' in the raw.orders dataset. This change affects any downstream consumers that reference 'order_total' by name, including dashboards, queries, and pipelines. These consumers will need to be updated to use the new column name to avoid errors.

## Why this is critical

This schema change is critical due to its impact on 8 downstream assets, with 3 tier1 assets that are business-critical. The highest criticality tier affected, tier1, indicates that this change poses a significant risk to business operations. If unmanaged, it could lead to data inconsistencies and disruptions across multiple business-critical systems.

## Downstream blast radius (8 assets)

| Asset | Tier | Hops | Owners |
|---|---|---|---|
| `staging.orders_enriched` | tier1 | 1 | sam.patel |
| `marts.order_facts` | tier2 | 2 | sam.patel |
| `marts.revenue_daily` | tier1 | 2 | sam.patel |
| `marts.customer_ltv` | tier2 | 2 | sam.patel |
| `marts.product_performance` | tier3 | 2 | sam.patel |
| `dashboards.customer_360` | tier2 | 3 | alex.kim |
| `dashboards.revenue_overview` | tier1 | 3 | alex.kim |
| `dashboards.product_insights` | tier3 | 3 | alex.kim |

## Compatibility verdicts

Impacted field(s): `order_total`

**2 need updates · 6 to review · 0 compatible**

| Consumer | Verdict | Why |
|---|---|---|
| `staging.orders_enriched` | ❌ Needs update | Carries `order_total` in its own schema — it reads the changed field directly and will break until updated. |
| `marts.order_facts` | ❌ Needs update | Carries `order_total` in its own schema — it reads the changed field directly and will break until updated. |
| `marts.revenue_daily` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |
| `marts.customer_ltv` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |
| `marts.product_performance` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |
| `dashboards.customer_360` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |
| `dashboards.revenue_overview` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |
| `dashboards.product_insights` | ⚠️ Review | Does not republish `order_total`, but sits downstream of the change — check whether its transformation reads the field without exposing it (aggregations and filters leave no trace in the output schema). |

> `Review` is not a synonym for safe. It means lineage proves the asset is downstream but its stored schema does not republish the changed field, so a transformation may still read it (`SUM(order_total) AS revenue` leaves no trace in the output schema). Only `needs update` is a confirmed break.

## Remediation plan (6 steps)

A critical schema migration for the raw.orders dataset is required to address breaking changes, impacting critical downstream assets staging.orders_enriched and marts.order_facts. Immediate action is needed to notify affected owners and update dependent consumers to prevent data inconsistencies and potential business disruptions. Failure to address this change will result in data integrity issues and potential revenue loss.

### 1. Notify owners of the 8 affected downstream asset(s)

breaking change on raw.orders (`order_total` renamed to `order_amount`) is assessed critical. Give owners advance notice before the change lands so they are not debugging a failure they were never told about.

Targets: `staging.orders_enriched`, `marts.order_facts`, `marts.revenue_daily`, `marts.customer_ltv`, `marts.product_performance`, `dashboards.customer_360`, `dashboards.revenue_overview`, `dashboards.product_insights`

Owners: sam.patel, alex.kim

### 2. Update 2 consumer(s) that reference `order_total`

These assets carry the changed field(s) in their own schema, so the reference is confirmed, not inferred. Update their transformations and tests to the new schema.

Targets: `staging.orders_enriched`, `marts.order_facts`

Owners: sam.patel

### 3. Review 6 downstream asset(s) for indirect references

These do not republish `order_total`, but a transformation can read a column without exposing it (aggregations, filters, joins). Grep their SQL/dbt models before assuming they are safe.

Targets: `marts.revenue_daily`, `marts.customer_ltv`, `marts.product_performance`, `dashboards.customer_360`, `dashboards.revenue_overview`, `dashboards.product_insights`

Owners: sam.patel, alex.kim

### 4. Regenerate lower-environment fixtures for raw.orders

Dev/QA fixtures still match the old schema (`order_total` renamed to `order_amount`). Regenerate schema-safe synthetic data against the new definition so lower environments stop drifting from production.

Targets: `raw.orders`

### 5. Re-run downstream tests against the new schema

Run the affected pipelines and dashboard queries against the regenerated fixtures. Treat a green run as the gate for shipping this change.

Targets: `staging.orders_enriched`, `marts.order_facts`

### 6. Publish the outcome back to DataHub

Write sync status, risk severity (critical) and the remediation PR link onto raw.orders so the next person to touch this dataset sees what happened and why.

Targets: `raw.orders`

## Regenerated fixtures (3 files, 600 rows)

Lower-environment test data regenerated against the **new** schema and validated with `pandera` (dtypes, nullability, primary-key uniqueness). Column values are generated from an inferred semantic type per column, not random fill.

| File | Why regenerated | Rows | Validation |
|---|---|---|---|
| `fixtures/marts.order_facts.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/raw.orders.csv` | the changed dataset | 200 | ✅ passed |
| `fixtures/staging.orders_enriched.csv` | confirmed to reference the changed field | 200 | ✅ passed |

<details><summary>Inferred column semantics — <code>marts.order_facts</code></summary>

| Column | Inferred meaning |
|---|---|
| `status` | ORDER_STATUS |
| `order_id` | IDENTIFIER |
| `item_count` | QUANTITY |
| `order_date` | DATE |
| `customer_id` | IDENTIFIER |
| `order_total` | CURRENCY_AMOUNT |

</details>

<details><summary>Inferred column semantics — <code>raw.orders</code></summary>

| Column | Inferred meaning |
|---|---|
| `currency` | CURRENCY_CODE |
| `order_id` | IDENTIFIER |
| `created_at` | TIMESTAMP |
| `updated_at` | TIMESTAMP |
| `customer_id` | IDENTIFIER |
| `order_amount` | CURRENCY_AMOUNT |
| `order_status` | ORDER_STATUS |

</details>

<details><summary>Inferred column semantics — <code>staging.orders_enriched</code></summary>

| Column | Inferred meaning |
|---|---|
| `order_id` | IDENTIFIER |
| `created_at` | TIMESTAMP |
| `item_count` | QUANTITY |
| `customer_id` | IDENTIFIER |
| `order_total` | CURRENCY_AMOUNT |
| `order_status` | ORDER_STATUS |
| `customer_email` | EMAIL |

</details>
