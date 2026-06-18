# Autonomous Integration Testing Specialist

A Claude skill that acts as an Autonomous Integration Testing Specialist, Environment
Provisioning Specialist, and TestOps Engineer. It analyzes a software project end to
end, validates that required dependencies and infrastructure are ready, provisions
missing resources safely, prepares realistic test data, runs ordered integration and
end-to-end tests, reconciles data across systems, and produces an evidence-backed
readiness or certification report.

Its guiding principle: **certify the platform, not only the target module.** A module is
not integration-ready unless its dependencies, data paths, workflows, and operational
checks are also ready.

## Package layout

| Path | Purpose |
|---|---|
| `SKILL.md` | Skill definition: purpose, operating rules, safety guardrails, the 13-phase workflow, and certification standards. Loaded first. |
| `config/skill-config.yaml` | All tunable parameters: business domains, infrastructure, environment checks, schema, dependencies, execution order, coverage, workflows, resilience, reconciliation, certification, safety, and reporting. |
| `references/phase-deliverables.md` | Per-phase checklist with objectives, sub-steps, config cross-references, and required deliverables. Loaded before running a phase or producing certification output. |
| `references/integration-scenarios-sop-template.md` | Fillable Continuous Integration Scenarios SOP template. The skill requests a completed SOP on every run and offers this template when the user has none. |
| `references/final-report-template.md` | Structure for the final certification report, including the per-call Function Results (input arguments + output) format. The skill writes the report into the target project's `docs/` directory. |
| `agents/interface.yaml` | Interface descriptor (display name, default prompt, invocation policy). |
| `output/`, `scripts/` | Created at runtime to hold generated artifacts (reports, creation/test scripts). |

## Invocation

Trigger the skill when a task involves assessing integration readiness, discovering
services/schemas/dependencies, preparing or running integration tests, validating
cross-system workflows, or certifying a release. Example:

> Use the Autonomous Integration Testing Specialist to assess integration readiness,
> prepare end-to-end tests, and produce an evidence-backed certification report.

It runs the full 13-phase certification by default, or a scoped subset when requested
(e.g. "validate the environment only"). It reuses and extends existing test assets when
they are current, and creates new ones only when none are found.

On every run the skill asks for a **Continuous Integration Scenarios SOP** — the
project-specific document that defines what to test, in what order, against which
environment, and what certification requires. If you don't have one, the skill offers
the fillable template at `references/integration-scenarios-sop-template.md` (or drafts
one from project discovery for your approval). Testing does not begin without a
confirmed SOP.

## Configuration precedence

1. Explicit user request
2. Existing project conventions
3. `.env` values
4. `config/skill-config.yaml`
5. Built-in defaults in `SKILL.md`

### Environment overrides

These keys (see `env_overrides` in the config) let `.env` values redirect local
defaults; user instructions always take precedence:

| Env var | Overrides |
|---|---|
| `AIT_CONFIG_PATH` | Config file location |
| `AIT_REFERENCE_DIR` | Reference directory |
| `AIT_SCRIPT_DIR` | Generated-script output directory |
| `AIT_OUTPUT_DIR` | Report output directory |
| `AIT_ENVIRONMENT_TARGET` | Target environment for provisioning |
| `AIT_CERT_REPORT_FORMAT` | Certification report format |

## Workflow at a glance

The skill executes in dependency order, not file-discovery order:

```text
Foundation -> Master Data -> Customer -> Product -> Pricing
           -> RFQ -> Quote -> Order -> Fulfillment -> Billing
```

The 13 phases: (1) Project Discovery, (2) Environment Discovery & Validation,
(3) Schema Discovery & Provisioning, (4) Dependency Discovery/Provisioning/
Configuration/Certification, (5) Dependency Relationship Analysis, (6) Integration
Testing Plan, (7) Test Asset Preparation, (8) Test Data Generation & Loading,
(9) Integration Test Script Generation, (10) End-to-End Transaction Testing,
(11) Failure & Resilience Testing, (12) Data Reconciliation, (13) Final Reporting &
Certification. See `references/phase-deliverables.md` for the deliverables of each.

## Safety

Provisioning and configuration run only in an authorized target environment. The skill
stops and requests explicit approval — with the exact command or change plan — before
any action involving privileged access, cloud credentials, production access,
destructive changes, or live third-party side effects. It never stores credentials,
tokens, API keys, or production connection strings in skill files, `.env`, generated
plans, or reports; it uses the project's approved secret source at runtime.

## Certification statuses

`Integration Certified` · `Ready for UAT` · `Ready for Production` ·
`Ready with Conditions` · `Not Ready`

Every status is grounded in observed evidence (code paths, config, command output, API
responses, database checks, logs, test results). Untested components are never implied
as certified; unverified items are marked `assumed`, `unavailable`, or `blocked`.
