# Arxyne gate demo: a merge refused on a simulation result

A saved simulation result blocks a merge, using the
[Arxyne GitHub Action](https://github.com/arxynehq/arxyne-ci).

| Branch | Check | Why |
|---|---|---|
| `main` | green | `cd = 0.0196` meets its target of `<= 0.025` |
| `violation` | **red** | the same result misses a tighter target of `<= 0.012` |

Same result, one declared number different — and that number flips the check.

## The result

`cd = 0.01963624`, `cl = 0.1766451`, `cm_pitch = -0.01052788` — a NASTRAN M6 wing solved with
OpenFOAM `simpleFoam`, 96,286 cells, converged at iteration 117. Written by the platform's own
writer and committed under `simulation_runs/`.

## The two twins

Both hold that same result with the same geometry and provenance. They differ in one attribute:
the target in `design_intent` (0.025 passing, 0.015 failing, `violation` tightens it to 0.012).
The gate judges a result against a declared intent and nothing else.

## Run it locally

No GPU and no Docker — the gate reads saved results.

```bash
pip install arxyne
export ARXYNE_HOME="$PWD/twins/failing"
arxyne results --product onera-m6 --json --fail-on-gate; echo "exit: $?"
```

That prints `exit: 3`. Use `twins/passing` for `exit: 0`.

## What runs here

- `gate` — the passing twin, `fail_on_gate: true`. Green.
- `gate-report-only` — the failing twin, `fail_on_gate: false`. Stays green and still names the miss.
- `gate-violation` — the failing twin on the `violation` branch only.

## Blocking the merge

The failing check makes the run red, so a protection rule requiring it cannot be satisfied. See
[docs/merge-block.md](docs/merge-block.md).

## Licence

MIT — see [LICENSE.md](LICENSE.md).
