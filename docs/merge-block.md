# Blocking a merge on a simulation result

Three levels, and only the last is a gate in the sense a safety process means.

## 1. The check fails (every tier)

The gate exits non-zero, so the run is red and a branch protection rule that requires the
check cannot be satisfied.

What it does not buy: an administrator can still override. A red check is a signal, not a lock.

## 2. The report names the failure (every tier)

The action emits one annotation per missed target and writes a job-summary table of every
gated KPI with its status. Both render on the run page, both are free, and neither needs a
marketplace reporter - which is why the action does not ship a third-party JUnit renderer.
The JUnit file is still written, and `actions/upload-artifact` publishes it for any
reporting tool you already run.

On this repo the failing twin returns one `::error` annotation naming the KPI, with the gate
engine's own message, and a summary table listing the row as `FAIL`. A reviewer sees which
requirement failed without opening a log.

Still no enforcement.

## 3. The merge is refused (every tier)

One setting: **Settings -> Branches -> Branch protection rule -> Require status checks to
pass**, with the gate job's check required. A red check then blocks the merge. Free plans can
apply branch protection to public repositories; private repositories need a paid plan.

**A required review adds a person, not the block.** The refusal comes from the status check.
A CODEOWNERS review buys a named sign-off recorded against the merge; it does not judge the
result, and it does not replace the gate.

## Why there is no ISO 26262 framework here

A standard like 26262 allows tool flexibility and certifies **process**, not only results. Two
companies can both be compliant with materially different controls. A vendor shipping a
complete framework would either dictate controls the standard does not require, or claim a
completeness it cannot have.

The gate plugs into a policy you already have.

## Setting it up

1. **Settings -> Branches -> Add branch protection rule** for `main`.
2. Tick **Require status checks to pass**, and select the gate job's check name.
3. Open a pull request from the `violation` branch. The check is red, and the merge is
   refused.

The GitLab counterpart of this page documents the same three levels there, where "Pipelines
must succeed" plays the role of the required check: [arxyne-gate-demo on
GitLab](https://gitlab.com/arxynehq/arxyne-gate-demo/-/blob/main/docs/merge-block.md).
