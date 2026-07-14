# Participation scheme (draft)

> **Status:** Working design notes from Michael — **not legal advice**, not a signed agreement.  
> Numbers and thresholds stay **intent** until formalised.

## Two separate tracks

| Track | What it is | How you get ownership |
|--------|------------|------------------------|
| **Company stake (equity)** | Up to **1% of the company** | Unlocked by **paid work hours** after gateway (not by buying a laptop) |
| **Laptop** | Device on **loan** from the business | Own only by **buying with £** at **value price** (valued at loan + depreciation) |

**Equity is never payment for the laptop. The laptop is never paid in shares.**

```text
  TRAIN (free)  →  GATEWAY (pass tests)  →  PAID WORK (typing)  →  pay £/hr
                                              │
                                              └→ optional: unlock company stake
                                                 (1% pot @ 0.2% per paid hour)

  TRAIN milestone (1hr audio + 5 Qs)  →  laptop LOAN
                                              │
                                              └→ optional: BUY with £ at value price
```

---

## Phase 1 — Training (free)

| Rule | Detail |
|------|--------|
| **Cost** | Free |
| **Time limit** | None — train as long as they like |
| **Activities** | Audios; multi-choice / practice questions; re-sit as needed |
| **Pay** | Not paid for training |
| **Equity** | Training does **not** unlock company stake |

---

## Phase 2 — Gateway (unlock paid work)

| Rule | Detail |
|------|--------|
| **What** | Formal tests |
| **Pass bar** | Must **score high enough** (threshold TBD) |
| **Retakes** | Keep training and re-attempt until pass |
| **Effect** | Unlocks eligibility for **paid work** only |

Does **not** by itself grant equity or laptop ownership.

---

## Phase 3 — Paid work (“once they are typing”)

| Rule | Detail |
|------|--------|
| **Trigger** | Gateway passed **and** assigned work (“typing”) |
| **Pay** | **Paid in money** for those hours (min wage or scheme rate) |
| **Not paid** | Pure training time without assigned work |

### Company stake (equity) — separate from laptop

For people who are **in** on the equity track:

| Idea | Detail |
|------|--------|
| **What** | A **stake in the company** — up to **1%** per person |
| **Not** | Not a claim on any laptop or other kit |
| **Unlock rate** | **0.2% of the company per hour** of **paid** work |
| **Full unlock of their 1%** | 1% ÷ 0.2%/hr = **5 hours** of paid work |
| **Training hours** | Do **not** count |

Legal form (options, growth shares, etc.) TBD with proper docs.

---

## Laptop scheme — loan, then buy with £ only

### Loan

| Step | Detail |
|------|--------|
| **Earn a loan** | Complete **1 hour of audio + 5 multi-choice questions** (training milestone) |
| **What they get** | A laptop **on loan** — business remains owner |
| **Return** | Returned if they leave or the loan ends, unless purchased |

### Valuation at loan

When the laptop is loaned out:

1. Record **value at loan** (e.g. purchase cost or agreed fair value on that day).  
2. That figure is the starting point for depreciation.

### Depreciation & sale price

| Rule | Detail |
|------|--------|
| **Ownership path** | **Only** by paying **£** — no equity, no free gift of title |
| **Price** | **Value price** = depreciated value under the formula |
| **Formula** | Depreciation from **value at loan** over time (exact schedule TBD) |

Example shape (illustrative only — not final):

```text
value_price(t) = value_at_loan × (1 − depreciation_rate × months_on_loan)
                 floored at residual_min (if any)
```

Or straight-line to a residual over N months. **Write the real numbers** in [laptop-valuation.md](../operations/laptop-valuation.md) when kit costs are known.

### Explicit non-rules

- Completing audio/questions does **not** transfer ownership.  
- Unlocking company stake does **not** transfer ownership of the laptop.  
- Paid work does **not** automatically buy the laptop — that is a **separate cash purchase** at value price.

---

## First meeting (Howden)

See [first business meeting](../operations/first-business-meeting.md).

Explain clearly:

1. Train free → gateway → **paid when typing**.  
2. **1% is a company stake**, unlocked by paid hours — **not** the laptop.  
3. Laptop is **loan**; own it only by **buying with £** at **depreciated value**.  

---

## Revision log

| Date | Note |
|------|------|
| 2026-07-14 | Initial capture |
| 2026-07-14 | Gateway + paid when typing |
| 2026-07-14 | **Split tracks:** 1% = company stake only; laptop = £ buy at value (loan valuation + depreciation) |
