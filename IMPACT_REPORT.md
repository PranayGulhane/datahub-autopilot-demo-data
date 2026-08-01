# Schema Change Impact Report — `raw.orders`

> `order_total` renamed to `order_amount`

| | |
|---|---|
| **Dataset** | `raw.orders` |
| **Classification** | `breaking` |
| **Risk** | 🔴 Critical |
| **Downstream assets affected** | 8 |
| **Consumers needing updates** | 6 |
| **Detected** | 2026-08-01 19:50 UTC+0530 |

*Generated automatically by DataHub Autopilot from DataHub lineage, ownership and schema metadata. Every figure below is computed, not estimated.*

## What changed

The 'order_total' column in the 'raw.orders' dataset has been renamed to 'order_amount'. This change is not structurally compatible, meaning that any downstream consumers (dashboards, queries, pipelines) that explicitly select this column by name will break and require updates to reference the new column name.

## Why this is critical

A critical schema change to raw.orders is warranted due to its impact on 8 downstream assets, with 3 tier1 assets (business-critical) being the most critical, including staging.orders_enriched, marts.revenue_daily, and dashboards.revenue_overview, which could lead to significant data inconsistencies and revenue loss if not properly managed.

## Downstream blast radius (8 assets)

| Asset | Tier | Hops | Owners |
|---|---|---|---|
| `staging.orders_enriched` | tier1 | 1 | sam.patel |
| `marts.customer_ltv` | tier2 | 2 | sam.patel |
| `marts.product_performance` | tier3 | 2 | sam.patel |
| `marts.order_facts` | tier2 | 2 | sam.patel |
| `marts.revenue_daily` | tier1 | 2 | sam.patel |
| `dashboards.customer_360` | tier2 | 3 | alex.kim |
| `dashboards.product_insights` | tier3 | 3 | alex.kim |
| `dashboards.revenue_overview` | tier1 | 3 | alex.kim |

## Compatibility verdicts

Impacted field(s): `order_total`

**6 need updates · 2 to review · 0 compatible**

| Consumer | Verdict | Why |
|---|---|---|
| `staging.orders_enriched` | ❌ Needs update | Column-level lineage derives `order_total` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |
| `marts.customer_ltv` | ❌ Needs update | Column-level lineage derives `avg_order_value`, `lifetime_value` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |
| `marts.product_performance` | ⚠️ Review | Column-level lineage traces no path from `order_total` to any field of this asset, and it does not republish the name — likely unaffected, but confirm the column lineage is complete before relying on that. |
| `marts.order_facts` | ❌ Needs update | Column-level lineage derives `order_total` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |
| `marts.revenue_daily` | ❌ Needs update | Column-level lineage derives `gross_revenue` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |
| `dashboards.customer_360` | ❌ Needs update | Column-level lineage derives `ltv` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |
| `dashboards.product_insights` | ⚠️ Review | Column-level lineage traces no path from `order_total` to any field of this asset, and it does not republish the name — likely unaffected, but confirm the column lineage is complete before relying on that. |
| `dashboards.revenue_overview` | ❌ Needs update | Column-level lineage derives `avg_order_value`, `total_revenue` from `order_total` — this consumer reads the changed field, whether or not it republishes the name. |

> `Review` is not a synonym for safe. It means lineage proves the asset is downstream but its stored schema does not republish the changed field, so a transformation may still read it (`SUM(order_total) AS revenue` leaves no trace in the output schema). Only `needs update` is a confirmed break.

## Remediation plan (6 steps)

A critical schema migration is required for the raw.orders dataset, impacting 8 downstream assets and 6 consumers. If not addressed, this change may cause data inconsistencies and errors in critical business applications, including customer 360 views and revenue reporting. The first step is to notify the owners of the affected assets, as timely action is necessary to prevent disruptions to our business operations.

### 1. Notify owners of the 8 affected downstream asset(s)

breaking change on raw.orders (`order_total` renamed to `order_amount`) is assessed critical. Give owners advance notice before the change lands so they are not debugging a failure they were never told about.

Targets: `staging.orders_enriched`, `marts.customer_ltv`, `marts.order_facts`, `marts.revenue_daily`, `dashboards.customer_360`, `dashboards.revenue_overview`, `marts.product_performance`, `dashboards.product_insights`

Owners: sam.patel, alex.kim

### 2. Update 6 consumer(s) that reference `order_total`

These assets carry the changed field(s) in their own schema, so the reference is confirmed, not inferred. Update their transformations and tests to the new schema.

Targets: `staging.orders_enriched`, `marts.customer_ltv`, `marts.order_facts`, `marts.revenue_daily`, `dashboards.customer_360`, `dashboards.revenue_overview`

Owners: sam.patel, alex.kim

### 3. Review 2 downstream asset(s) for indirect references

These do not republish `order_total`, but a transformation can read a column without exposing it (aggregations, filters, joins). Grep their SQL/dbt models before assuming they are safe.

Targets: `marts.product_performance`, `dashboards.product_insights`

Owners: sam.patel, alex.kim

### 4. Regenerate lower-environment fixtures for raw.orders

Dev/QA fixtures still match the old schema (`order_total` renamed to `order_amount`). Regenerate schema-safe synthetic data against the new definition so lower environments stop drifting from production.

Targets: `raw.orders`

### 5. Re-run downstream tests against the new schema

Run the affected pipelines and dashboard queries against the regenerated fixtures. Treat a green run as the gate for shipping this change.

Targets: `staging.orders_enriched`, `marts.customer_ltv`, `marts.order_facts`, `marts.revenue_daily`, `dashboards.customer_360`, `dashboards.revenue_overview`

### 6. Publish the outcome back to DataHub

Write sync status, risk severity (critical) and the remediation PR link onto raw.orders so the next person to touch this dataset sees what happened and why.

Targets: `raw.orders`

## Regenerated fixtures (7 files, 1400 rows)

Lower-environment test data regenerated against the **new** schema and validated with `pandera` (dtypes, nullability, primary-key uniqueness). Column values are generated from an inferred semantic type per column, not random fill.

| File | Why regenerated | Rows | Validation |
|---|---|---|---|
| `fixtures/dashboards.customer_360.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/dashboards.revenue_overview.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/marts.customer_ltv.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/marts.order_facts.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/marts.revenue_daily.csv` | confirmed to reference the changed field | 200 | ✅ passed |
| `fixtures/raw.orders.csv` | the changed dataset | 200 | ✅ passed |
| `fixtures/staging.orders_enriched.csv` | confirmed to reference the changed field | 200 | ✅ passed |

<details><summary>Inferred column semantics — <code>dashboards.customer_360</code></summary>

| Column | Inferred meaning |
|---|---|
| `segment` | GENERIC_STRING |
| `full_name` | PERSON_NAME |
| `customer_id` | IDENTIFIER |
| `total_orders` | QUANTITY |
| `lifetime_value` | CURRENCY_AMOUNT |

</details>

<details><summary>Inferred column semantics — <code>dashboards.revenue_overview</code></summary>

| Column | Inferred meaning |
|---|---|
| `metric_date` | DATE |
| `total_orders` | QUANTITY |
| `total_revenue` | CURRENCY_AMOUNT |
| `avg_order_value` | CURRENCY_AMOUNT |

</details>

<details><summary>Inferred column semantics — <code>marts.customer_ltv</code></summary>

| Column | Inferred meaning |
|---|---|
| `full_name` | PERSON_NAME |
| `customer_id` | IDENTIFIER |
| `total_orders` | GENERIC_NUMBER |
| `lifetime_value` | CURRENCY_AMOUNT |
| `avg_order_value` | CURRENCY_AMOUNT |
| `last_order_date` | DATE |

</details>

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

<details><summary>Inferred column semantics — <code>marts.revenue_daily</code></summary>

| Column | Inferred meaning |
|---|---|
| `net_revenue` | CURRENCY_AMOUNT |
| `order_count` | QUANTITY |
| `revenue_date` | DATE |
| `gross_revenue` | CURRENCY_AMOUNT |
| `refund_amount` | CURRENCY_AMOUNT |

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
