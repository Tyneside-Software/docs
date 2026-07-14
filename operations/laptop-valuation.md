# Laptop valuation (draft)

> Used when a device is **loaned**, then optionally **sold for £** at **value price**.  
> Not related to company equity.

## Principles

1. **Value at loan** — set and recorded when the laptop is issued.  
2. **Depreciation formula** — reduces value over time while on loan.  
3. **Sale price** — participant pays **value price** in **£** only (no shares, no barter for equity).  
4. Until sale (or return), **title stays with the business**.

## Fields to record per device

| Field | Example |
|--------|---------|
| Asset id | `LAP-001` |
| Model / serial | … |
| Value at loan (£) | e.g. purchase invoice or agreed fair value |
| Loan start date | … |
| Borrower | … |
| Depreciation method | TBD (e.g. straight-line monthly) |
| Residual floor (£) | TBD (optional minimum) |
| Value price if sold today | `f(value_at_loan, elapsed, formula)` |

## Formula (placeholder)

```text
value_price = max(residual_floor, value_at_loan − (monthly_depreciation × full_months_elapsed))
```

or

```text
value_price = max(residual_floor, value_at_loan × (1 − monthly_rate × full_months_elapsed))
```

**Fill in:** `monthly_depreciation` or `monthly_rate`, `residual_floor`, and whether partial months count.

## Related

- [Participation scheme](../people/participation-scheme.md) — loan milestone and equity kept separate  
