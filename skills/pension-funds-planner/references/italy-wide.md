# Italy-wide planning rules

Use this reference for any request that falls within Italian complementary pension funds (`previdenza complementare`).

## Shared assumptions

- Confirm the annual deductible cap for the current tax year instead of hardcoding it forever.
- The deductible headroom should be modeled across the relevant Italian pension-fund contributions, not fund by fund in isolation.
- Contributions above the deductible cap may still be paid into the fund, but they usually lose the immediate tax benefit and may require later reporting as non-deductible amounts.
- When payroll inputs are uncertain, prefer leaving a buffer under the cap.

## Payroll timing rules

- Use the year in which the contribution is actually credited, not the performance year of the bonus.
- Bonus or `variabile` paid in year N+1 usually affects pension-fund contributions in year N+1.
- Salary increases and retroactive adjustments can distort the current year because they change the contribution base or create catch-up contributions.
- `Tredicesima` can create an extra December contribution if it is part of the contributable base in that setup.

## Employer-contribution rules

- Do not assume the employer contribution is calculated on the employee's full actual gross salary.
- In some setups the employer contribution is based on a narrower contractual base, so observed payroll amounts take priority over naive percentage-of-RAL math.
- If dropping below a threshold loses the employer contribution, quantify that tradeoff instead of treating the match as automatically worth keeping.

## Multiple funds

- If the user has multiple Italian pension funds, include all relevant Italian contributions in the same cap model.
- If the user also has foreign pension plans, exclude them from the Italian deductible-cap calculation.

## Verification standard

This skill provides practical planning support, not legal or tax advice. Before recommending an exact rate, confirm the current cap, the contribution windows, the employer-match threshold, and the actual payroll base with payroll, HR, or the fund documentation.
