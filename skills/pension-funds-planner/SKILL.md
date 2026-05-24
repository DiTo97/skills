---
name: pension-funds-planner
description: Plan contributions for complementary pension funds without exceeding the annual tax-deductible cap. Use whenever the user mentions Fondo Cometa or another fondo pensione, previdenza complementare, pensione integrativa, deduzione, RAL, bonus or variabile, tredicesima, employer contribution thresholds, contribution changes, or wants to stay just under the cap. Check `references/funds/index.md` for supported countries before proceeding.
argument-hint: "funds, salary, and contribution scenario"
---

# Pension Funds Planner

Help users plan contributions across one or more Italian complementary pension funds so they use the annual deductible headroom efficiently without drifting into unwanted non-deductible overflow.

Treat this as practical planning support, not legal or tax advice. Make assumptions explicit and tell the user what must be confirmed with payroll, HR, or the fund itself before they submit any form.

## Scope and routing

1. Start by checking whether the case is about a supported country in `references/funds/index.md`.
2. If the user asks about a pension fund or tax system from an unsupported country, stop and say this skill is out of scope for now and is a future TODO.
3. If the user has both Italian and non-Italian plans, calculate only the Italian portion and clearly exclude the foreign plan from the Italian deductible-cap math.
4. Read `references/italy-wide.md` for the shared Italy-wide rules.
5. Read `references/funds/index.md` to map the user's country and fund name to a bundled reference.
6. If a supported fund is matched, read that fund file before recommending a rate.
7. If the fund is Italian but does not have a bundled reference yet, continue in generic Italy mode, ask the user for fund-specific thresholds and deadlines, and say that adding a reference for that fund is still a TODO.

## Language

- Do not assume the user will write in English; users may ask in English or in the local language for the supported country.
- Accept both English and Italian inputs naturally for Italy/Italia cases.
- Preserve Italian payroll and pension terms such as `RAL`, `variabile`, `tredicesima`, `deduzione`, `contribuzione datoriale`, `minimo contrattuale`, or `superminimo`.
- Mirror the user's language when practical; if the user is clearly more comfortable in Italian, it is fine to answer in Italian.

## What to optimize for

Unless the user says otherwise, optimize in this order:

1. Avoid non-deductible overflow above the annual cap.
2. Keep a small safety buffer under the cap.
3. Preserve employer contributions only when they are economically worthwhile.
4. Keep the reasoning simple and action-oriented.

Default safety buffer: 100 EUR under the cap.
If the user wants a tighter or wider margin, use their number.

## Intake flow

Collect only the missing inputs that matter.

### Shared inputs

1. Current tax year.
2. Annual deductible cap for that year.
3. Country and tax context, if not already clear.
4. One fund or multiple Italian funds.
5. Contributions already credited in the current year, split by fund if possible into:
   - employee contributions
   - employer contributions
   - other deductible or voluntary contributions
6. Preferred buffer under the cap.

### Rate and employer-match inputs

For each relevant Italian fund, collect:

1. Current employee contribution rate.
2. Minimum employee rate needed to keep employer contributions.
3. Whether the user is willing to go below that threshold and lose the employer contribution.
4. Whether the employer contribution is based on full gross salary or on a narrower contractual base.

### Payroll structure

1. Fixed annual gross salary (`RAL`).
2. Number of salary payments in the year.
3. Current month and the next contribution-change deadline that matters.
4. Any recent salary increase or retroactive payroll adjustment already paid or still expected.
5. Whether December includes a contributable `tredicesima`.

### Variable compensation

1. Expected bonus or `variabile` amount.
2. Expected payment month.
3. Which contribution year that payment will hit.
4. Whether it follows the same employee rate as normal payroll.

## Calculation model

Use the current year unless the user explicitly wants next-year planning.

### Step 1: Aggregate the Italian deductible headroom

```text
used_headroom = sum(all_italian_employee_YTD + all_italian_employer_YTD + all_italian_other_deductible_YTD)
planning_target = annual_cap - safety_buffer
remaining_to_target = planning_target - used_headroom
remaining_to_legal_cap = annual_cap - used_headroom
```

The annual deductible cap is shared across the relevant Italian pension-fund contributions, so do not model each Italian fund in isolation.

### Step 2: Separate locked contributions from adjustable ones

Break the rest of the year into:

1. locked payrolls before the next effective change date
2. adjustable payrolls after the change date
3. special payroll items such as bonus payouts, retroactive pay, and `tredicesima`

Forecast each item into the year in which the contribution will actually be credited.

### Step 3: Forecast the no-change path

```text
projected_total_no_change = used_headroom + future_employee_current_rate + future_employer_current_rules + future_other_deductible
```

Use this to estimate the likely undershoot or overflow.

### Step 4: Solve for the adjustable rate

Only use the future adjustable contribution base when solving for a post-deadline employee rate.

```text
needed_employee_after_change = planning_target - used_headroom - unavoidable_future_employee - unavoidable_future_employer - adjustable_future_employer - future_other_deductible_except_adjustable_employee
suggested_rate = needed_employee_after_change / adjustable_contribution_base
```

Then compare at least these scenarios:

1. no change
2. keep employer contribution using the minimum qualifying rate
3. cap-safe rate based on the calculation above
4. conservative underfill if payroll volatility is high

## Decision rules

- Prefer staying under the cap when bonus size, retroactive pay, or employer-contribution rules are uncertain.
- Do not treat employer contribution as automatically worth preserving; quantify the tradeoff.
- Trust observed payroll evidence over naive percentage-of-RAL assumptions when the two disagree.
- Separate short-term action from next-year planning when a fund has mid-year and end-of-year change windows.
- Do not invent fund-specific deadlines, thresholds, or formulas. If the bundled reference does not cover them, ask the user to confirm them.

## Report structure

Always answer in this structure.

### 1. Summary

State the likely outcome with the current setup and the recommended next move.

### 2. Assumptions

List the cap, funds included, YTD contributions, salary structure, bonus timing, employer-threshold rule, and safety buffer.

### 3. Forecast

Show:

- used headroom now
- projected additional contributions before the next effective change date
- projected additional contributions after the change date
- projected year-end total under each relevant scenario

### 4. Rate options

Present a short table with:

- fund or combined rate context
- keeps employer contribution: yes or no
- projected year-end total
- buffer or overflow vs cap
- comment

### 5. Recommendation

State the recommended rate and why it best matches the user's objective.

### 6. Verify before submitting

List the items the user should confirm with payroll, HR, or the fund.

## Out-of-scope response pattern

If the user asks about a non-Italian pension fund or a non-Italian tax system, reply clearly that this skill currently supports Italy only. Offer to help with:

- a generic savings-allocation discussion without country-specific rules, or
- collecting the foreign-fund rules so the skill can be extended later.

## Example prompts that should trigger this skill

- "I’m in Fondo Cometa and want to stay under the deductible cap after a salary increase."
- "Help me reason about my Italian pension fund contributions, bonus timing, and tredicesima."
- "I have more than one fondo pensione in Italy. Can you model the shared cap across them?"
- "If I drop below the employer-match threshold, is it still worth it to avoid non-deductible overflow?"
- "I know the fund but not the right rules. Interview me quickly and tell me what rate I should set."
