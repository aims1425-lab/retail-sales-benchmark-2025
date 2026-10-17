# Controlled Synthetic Retail Sales — 2025 Preview

A small **controlled synthetic fixture** for CSV joins, grouped aggregation,
pipeline checks, and dashboard demos. Every store, product, sale, price, and
return is fictional; there is no PII or observed retail data. `USD` is only a test
denomination, not our income. This is not a forecast, accounting ledger,
real-market benchmark, or evidence of realistic demand.

## Files and coverage

| File | Data rows | Grain / key |
| --- | ---: | --- |
| [`data/daily_sales.csv`](data/daily_sales.csv) | 84 | One date × store × product; key `(date,store_id,product_id)` |
| [`data/stores.csv`](data/stores.csv) | 2 | Fictional store; key `store_id` |
| [`data/products.csv`](data/products.csv) | 3 | Fictional product; key `product_id` |

The preview selects existing rows, without changing their values: the first 14
Gregorian days, **2025-01-01 through 2025-01-14** inclusive; the first two sorted
store IDs, **`S001`, `S002`**; and the first three sorted product IDs, **`P001`,
`P002`, `P003`**. All 14 × 2 × 3 combinations remain, including one zero-unit row.
Both dimensions contain only those IDs. Daily rows are sorted by date, store ID,
then product ID; dimensions are sorted by ID. This January-only slice does not
represent all patterns in the full year.

## Schema and import conventions

CSVs use commas, standard minimal quoting, strict **UTF-8 without BOM**, LF line
endings, and a final newline. Headers are exact and ordered; all cells are
required and non-null. Keep IDs as text, including their letter prefixes and
leading zeros. Integer fields contain unsigned decimal text; booleans are
lowercase `true`/`false`. Factors are dimensionless six-decimal text, not money.

`daily_sales.csv` has these **18 fields, in order**:

| Field | Meaning / representation |
| --- | --- |
| `date` | Gregorian `YYYY-MM-DD`; preview dates only; no time or timezone |
| `store_id` | Text foreign key to `stores.csv` |
| `product_id` | Text foreign key to `products.csv` |
| `currency` | Literal `USD`, a test denomination |
| `units_sold` | Nonnegative integer artificial unit count |
| `units_returned` | Integer from zero through `units_sold`; same-day synthetic returns |
| `unit_price_cents` | Positive integer test cents; equals the product dimension price |
| `discount_bps` | Integer discount basis points; 100 bps = 1% |
| `net_unit_price_cents` | Per-unit discounted price, rounded half up to integer cents |
| `gross_sales_cents` | `units_sold * unit_price_cents` |
| `discount_cents` | `units_sold * (unit_price_cents - net_unit_price_cents)` |
| `sales_cents` | `gross_sales_cents - discount_cents`, before refunds |
| `refund_cents` | `units_returned * net_unit_price_cents` |
| `net_sales_cents` | `sales_cents - refund_cents`, not real revenue |
| `weekday_factor` | Designed weekday demand multiplier; six-decimal text |
| `seasonal_factor` | Designed monthly step multiplier; `0.750000` in this January slice |
| `promotion` | Boolean text; an artificial scheduled promotion, not a real campaign |
| `is_synthetic` | Literal `true` on every row |

The exact dimension headers are:

- `stores.csv`: `store_id,store_name,demand_index_bps,is_synthetic`.
  `store_name` is a `SYNTH` label; `demand_index_bps` is a designed dimensionless
  scale relative to 10000, not a discount or measured demand.
- `products.csv`: `product_id,product_name,category,unit_price_cents,base_daily_units,is_synthetic`.
  Names and categories are `SYNTH` labels; prices and base counts are arbitrary
  test constants. `is_synthetic` is always `true` in both dimensions.

All monetary fields are **integer cents**, not binary floating-point dollars.
Compute `net_unit_price_cents` by rounding
`unit_price_cents * (10000 - discount_bps) / 10000` half up **before** multiplying
by units. Do not separately round a discount percentage of total gross. Returns
use the same row's net unit price; there are no later-day return cohorts, taxes,
fees, costs, inventory movements, or cash reconciliation.

### Small aggregation check

Summing the supplied preview, including its zero-unit row:

| `store_id` | `units_sold` | `units_returned` | `net_sales_cents` |
| --- | ---: | ---: | ---: |
| `S001` | 488 | 24 | 209370 |
| `S002` | 484 | 18 | 208534 |
| **Total** | **972** | **42** | **417904** |

These are calculated synthetic test totals, not observed sales or earned money.
A join to either dimension must retain 84 daily rows without unmatched keys.

## Scope, provenance, and limits

The separate private full package has 73,000 daily rows over 365 days, 10 stores,
20 products, three CSVs, a generator, a data dictionary, and tests. That package
and its license are **not published here**; this repository is not a checkout,
delivery service, or statement that a purchase is available.

This preview was authored, validated, and reviewed automatically with AI. It is
supplied under `aims1425-lab` by **Jarvis, an authorized automated assistant**.
Local automated checks covered the exact source-row subset, headers, complete
key grid, joins, row order, money identities, encoding, nulls, and formula-leading
cells. **No human QA was performed. Excel and Google Sheets compatibility has
not been tested.** These checks do not promise production suitability, security,
or defect-free operation.

## Permission and optional feedback

The [preview license](LICENSE.txt) grants non-exclusive, royalty-free permission
to use, copy, modify, redistribute, and fork these sample data and documents,
including commercial uses, only to the extent of actual licensable rights.
Preserve the synthetic, AI-authorship, and license notices. No exclusive AI-output
copyright or third-party rights are asserted. The grant does not relicense the
rest of the private package. Public copies and forks can persist; we cannot
promise to retrieve or remove every copy.

For a bug, schema question, or voluntarily initiated expression of interest,
[open an optional project issue](../../issues/new?template=nonbinding-scope.md)
or read the [same-repository issue template](.github/ISSUE_TEMPLATE/nonbinding-scope.md).
Issues are public: do not include PII, secrets, credentials, or client files.
This channel is nonbinding, not a contract, reservation, sale, quote, or payment
request. Scope, license, price, timeline, and any lawful fiat collection method
must be qualified and agreed before any commitment. There is no continuous
monitoring or response-time promise.
