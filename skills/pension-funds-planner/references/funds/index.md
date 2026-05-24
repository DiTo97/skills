# Supported pension-fund references

Use this index to map the user's country and fund name to a bundled reference.

| Country | Fund | Aliases | Coverage | File |
| --- | --- | --- | --- | --- |
| Italy (`Italia`) | Fondo Cometa | `Cometa` | Starter reference | `fondo-cometa.md` |

## Routing notes

- First check whether the user is asking about a country listed in the `Country` column.
- If the user names `Italy`, `Italia`, `Fondo Cometa`, or `Cometa`, read `fondo-cometa.md` before calculating a recommendation.
- If the user names another Italian pension fund that is not listed here, stay in generic Italy mode and ask for the fund-specific thresholds, deadlines, and payroll rules instead of inventing them.
- If the user names a pension fund from an unsupported country, stop and state that this skill currently supports Italy/Italia only.
