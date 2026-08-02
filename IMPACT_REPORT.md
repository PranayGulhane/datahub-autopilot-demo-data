# Schema Change Impact Report — `raw.products`

> `weight_kg` added

| | |
|---|---|
| **Dataset** | `raw.products` |
| **Classification** | `additive` |
| **Risk** | 🟢 Safe |
| **Downstream assets affected** | 3 |
| **Consumers needing updates** | 0 |
| **Detected** | 2026-08-02 14:40 UTC+0000 |

*Generated automatically by DataHub Autopilot from DataHub lineage, ownership and schema metadata. Every figure below is computed, not estimated.*

## What changed

The raw.products dataset has been updated with a new column called weight_kg. This column represents the weight of each product in kilograms. Downstream consumers can now use this information to perform new calculations or filtering, such as sorting products by weight or calculating total weight for a set of products.

## Why this is safe

This schema change is classified as safe due to its additive nature and the fact that it only affects three downstream assets, all of which are in the tier3 criticality tier. The tier3 criticality tier is the lowest criticality tier, which further justifies the safe severity. The change does not pose a significant risk to the business.

## Downstream blast radius (3 assets)

| Asset | Tier | Hops | Owners |
|---|---|---|---|
| `staging.products_dim` | tier3 | 1 | sam.patel |
| `marts.product_performance` | tier3 | 2 | sam.patel |
| `dashboards.product_insights` | tier3 | 3 | alex.kim |

## Compatibility verdicts

**0 need updates · 0 to review · 3 compatible**

| Consumer | Verdict | Why |
|---|---|---|
| `staging.products_dim` | ✅ Compatible | No field this consumer could be reading was removed, renamed, or retyped. |
| `marts.product_performance` | ✅ Compatible | No field this consumer could be reading was removed, renamed, or retyped. |
| `dashboards.product_insights` | ✅ Compatible | No field this consumer could be reading was removed, renamed, or retyped. |

> `Review` is not a synonym for safe. It means lineage proves the asset is downstream but its stored schema does not republish the changed field, so a transformation may still read it (`SUM(order_total) AS revenue` leaves no trace in the output schema). Only `needs update` is a confirmed break.

## Remediation plan (3 steps)

This schema migration plan updates the raw.products dataset with a non-disruptive change, classified as additive, which does not pose any risk to existing consumers. The first step in this process involves regenerating lower-environment fixtures for raw.products, setting the stage for downstream tests to be re-run against the new schema. If this change is skipped, the fixtures will remain outdated, potentially causing issues with data integrity and downstream workflows.

### 1. Regenerate lower-environment fixtures for raw.products

Dev/QA fixtures still match the old schema (`weight_kg` added). Regenerate schema-safe synthetic data against the new definition so lower environments stop drifting from production.

Targets: `raw.products`

### 2. Re-run downstream tests against the new schema

Run the affected pipelines and dashboard queries against the regenerated fixtures. Confirms nothing depended on the old shape implicitly.

Targets: `raw.products`

### 3. Publish the outcome back to DataHub

Write sync status, risk severity (safe) and the remediation PR link onto raw.products so the next person to touch this dataset sees what happened and why.

Targets: `raw.products`

## Regenerated fixtures (1 files, 200 rows)

Lower-environment test data regenerated against the **new** schema and validated with `pandera` (dtypes, nullability, primary-key uniqueness). Column values are generated from an inferred semantic type per column, not random fill.

| File | Why regenerated | Rows | Validation |
|---|---|---|---|
| `fixtures/raw.products.csv` | the changed dataset | 200 | ✅ passed |

<details><summary>Inferred column semantics — <code>raw.products</code></summary>

| Column | Inferred meaning |
|---|---|
| `sku` | SKU |
| `name` | PRODUCT_NAME |
| `price` | CURRENCY_AMOUNT |
| `category` | PRODUCT_CATEGORY |
| `in_stock` | BOOLEAN_FLAG |
| `weight_kg` | WEIGHT |
| `created_at` | TIMESTAMP |
| `product_id` | IDENTIFIER |

</details>
