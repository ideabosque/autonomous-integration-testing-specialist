# Final Integration Testing Certification Report — Template

> **How to use this template.** This is the structure for the final report the skill
> writes into the **target project's `docs/` directory** (for example,
> `docs/integration_certification_report.md`, or a dated name such as
> `docs/live_integration_results_<YYYYMMDD>.md`). Fill every `<placeholder>`, and record
> the **input arguments and output for every function/tool/API call** in the Function
> Results section. Keep `assumed`/`unavailable`/`blocked` labels for anything not
> observed. Delete these guidance blockquotes in the generated report.

---

- Generated at: `<ISO-8601 timestamp, e.g. 2026-06-17T20:50:06+00:00>`
- Project / module: `<system under test>`
- Business domain: `<ecommerce | logistics | finance | healthcare | iot | generic>`
- Environment target: `<dev | staging | qa | pre-prod>`
- Gateway / base URL: `<e.g. http://localhost:8765>`
- Endpoint: `<e.g. gpt>`
- Partition / namespace: `<e.g. nestaging>`
- Interface URL: `<e.g. GraphQL/REST endpoint URL>`
- SOP reference: `<path/version of the Integration Scenarios SOP this run executed>`
- Dependency / execution order: `<comma-separated order actually used>`
- Passed: `<n>`
- Failed: `<n>`
- Error responses: `<n>`
- Skipped: `<n>`
- Blocked: `<n>`
- Total calls: `<n>`
- **Final certification status:** `<Integration Certified | Ready for UAT | Ready for Production | Ready with Conditions | Not Ready>`

## Executive Summary

> 3–6 sentences: what was certified, against which environment, the headline result,
> and any blocking issues. State the certification decision and the single most
> important reason for it.

## Scope

- **In scope:** `<modules / services / workflows tested>`
- **Out of scope:** `<excluded areas>`
- **Phases executed:** `<e.g. 1–13, or the subset run>`
- **Phases assumed / skipped:** `<list, with reason>`

## Dependency Readiness

| Dependency | Type | Available | Configured | Initialized | Operational | Notes |
|---|---|---|---|---|---|---|
| `<customer_management>` | internal | ✅ | ✅ | ✅ | ✅ | `<evidence ref>` |
| `<postgresql>` | infrastructure | ✅ | ✅ | ✅ | ✅ | `<migration check>` |
| `<payment_gateway>` | external | ✅ | ✅ | ⚠️ | ❌ | `<blocked: sandbox down>` |

## Function Results

> One block per call, in execution order. Record the exact input **Arguments** and the
> returned **Output** as JSON. Truncate very large outputs with a clear `... (truncated)`
> marker and keep the structurally relevant portion. Set Status to `pass`, `fail`,
> `error`, `skipped`, or `blocked`.

### 1. `<group>` / `<method>` (`<short description>`)

- Method: `<method_name>`
- Status: `<pass | fail | error | skipped | blocked>`
- Elapsed: `<duration, e.g. 3558.19 ms>`
- Scenario ID: `<SOP scenario ref, e.g. INT-001>`

Arguments:

```json
{
  "<input arg>": "<value>"
}
```

Output:

```json
{
  "<output field>": "<value>"
}
```

> On failure, also include:

Expected:

```json
{ "<expected shape or value>": "..." }
```

Error / diff:

```text
<error message, status code, or expected-vs-actual diff>
```

### 2. `<group>` / `<method>` (`<short description>`)

- Method: `<method_name>`
- Status: `<...>`
- Elapsed: `<...>`
- Scenario ID: `<...>`

Arguments:

```json
{ }
```

Output:

```json
{ }
```

> Repeat for every call executed during the run.

## End-to-End Workflow Validation

| Workflow | Steps executed | Validation points | Result |
|---|---|---|---|
| `<Order Lifecycle>` | `<create → allocate → ship → invoice → pay>` | `<order_created, inventory_allocated, ...>` | `<pass / fail>` |

## Failure and Resilience Results

| Scenario | Injected fault | Expected behavior | Observed behavior | Result |
|---|---|---|---|---|
| `<api_failures>` | `<downstream 500>` | `<circuit breaker / retry>` | `<observed>` | `<pass / fail>` |

## Data Reconciliation

| Check | Rule | Tolerance | Observed | Result |
|---|---|---|---|---|
| `<cross-system consistency>` | `<order total == invoice total>` | `<amount: 0.01>` | `<delta>` | `<pass / fail>` |

## Coverage Analysis

| Area | Covered | Total | % | Notes |
|---|---|---|---|---|
| API operations | `<n>` | `<n>` | `<%>` | |
| Database operations | `<n>` | `<n>` | `<%>` | |
| Event operations | `<n>` | `<n>` | `<%>` | |
| Workflow operations | `<n>` | `<n>` | `<%>` | |

## Defect Analysis

| ID | Severity | Title | Root cause | Affected call(s) | Recommendation |
|---|---|---|---|---|---|
| `<DEF-001>` | `<blocking / critical / major / minor / informational>` | `<...>` | `<...>` | `<function ref>` | `<...>` |

## Open Risks and Mitigation Plan

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| `<...>` | `<low / med / high>` | `<low / med / high>` | `<...>` | `<...>` |

## Certification Decision

- **Status:** `<Integration Certified | Ready for UAT | Ready for Production | Ready with Conditions | Not Ready>`
- **Rationale:** `<grounded in the results above>`
- **Conditions (if any):** `<caveats that must be resolved>`
- **Evidence sources:** `<code paths, configs, command outputs, API responses, DB checks, logs>`

## Sign-off

| Role | Name | Date | Decision |
|---|---|---|---|
| Test owner | `<...>` | `<...>` | `<...>` |
| Release manager | `<...>` | `<...>` | `<...>` |
