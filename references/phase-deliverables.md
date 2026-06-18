# Phase Deliverables

Use this checklist when a task asks for full autonomous integration testing, readiness assessment, or production certification. Each phase lists its objective, sub-steps, config references, and required deliverables. Track missing deliverables as gaps when certifying readiness.

Config references point to `config/skill-config.yaml` paths. Precedence: (1) Explicit user request -> (2) Existing project conventions -> (3) `.env` values -> (4) `config/skill-config.yaml` -> (5) Built-in defaults.

## 1. Project Discovery and Assessment

**Objective:** Understand the complete project architecture and testing readiness.

**Step 1.1 - Project Analysis**
Analyze source code, architecture documentation, infrastructure definitions, API specifications, workflow definitions, deployment configurations, CI/CD configurations, and security configurations.
Discover modules, services, APIs, databases, event processors, queues, workflow engines, external integrations, and authentication providers.
Identify the business domain (see `business_domain.supported_domains`) to determine applicable execution sequences and dependencies.

**Step 1.2 - Existing Testing Asset Discovery**
Search for integration testing plans, previous testing reports, test execution results, QA documentation, automated test scripts, regression suites, defect reports, and test datasets.
If existing assets are found, perform coverage analysis, gap analysis, regression impact analysis, architecture change analysis, and defect trend analysis. Rebuild the testing strategy, enhance coverage, expand workflow validation, and add regression scenarios.
If no testing assets exist, create a new integration testing strategy, testing plan, scenarios, and assets.

**Deliverables:**

- Project Architecture Assessment Report
- Testing Readiness Assessment Report

## 2. Environment Discovery and Validation

**Objective:** Ensure all infrastructure required by the project exists and is operational.

**Step 2.1 - Infrastructure Discovery**
Discover compute resources (see `infrastructure.compute_platforms`), data resources (see `infrastructure.data_stores`), integration resources (see `infrastructure.integration_middleware`), and storage resources (see `infrastructure.storage_systems`).
When validating data store capabilities (transactions, foreign keys, stored procedures, views, full-text search), consult `infrastructure.data_store_capabilities` to adapt the test strategy to the target store's feature set.

**Step 2.2 - Environment Validation**
Validate environment variables, credentials, secrets, API endpoints, connection strings, security settings, and service health (see `environment.validation_checkpoints`).
On transient validation failures, retry up to `environment.validation_retries` times with `environment.health_check_timeout` per attempt.
If validation fails, respect `environment.block_on_failure` - do not proceed to testing.

**Deliverables:**

- Infrastructure Inventory Report
- Environment Validation Report

## 3. Schema Discovery and Provisioning

**Objective:** Validate and provision all required database structures before testing.

**Step 3.1 - Schema Discovery**
Identify databases, schemas, tables, collections, indexes, constraints, views, and stored procedures (see `schema.include_stored_procedures`).

**Step 3.2 - Schema Validation**
Validate database, table, collection, index, and constraint existence. Validate columns, data types, keys, relationships, and index definitions.

**Step 3.3 - Missing Resource Provisioning**
If missing and authorized, create databases, tables, collections, indexes, constraints, and views (see `schema.provisionable_resources`). Respect `schema.require_approval_for_destructive` for any drop/recreate operations.

**Step 3.4 - Schema Certification**
Revalidate accessibility, referential integrity, index readiness, and query readiness (see `schema.certification_checks`).

**Deliverables:**

- Schema Inventory Report
- Schema Validation Report
- Database Creation Scripts
- Schema Provisioning Report
- Schema Certification Report

## 4. Dependency Discovery, Provisioning, and Configuration

**Objective:** Discover, provision, configure, initialize, and certify all dependencies required by the target module. Testing may not proceed until all dependencies pass readiness certification.

**Step 4.1 - Dependency Discovery**
Discover internal dependencies (see `dependency.internal_categories`), infrastructure dependencies (see `dependency.infrastructure_categories`), and external dependencies (see `dependency.external_categories`).
For domain-specific core dependencies, consult `business_domain.domain_configs.<domain>.core_dependencies`.

**Step 4.2 - Dependency Validation**
Validate service deployment, service health, endpoint availability, API accessibility, and authentication configuration.
Apply `dependency.health_check_timeout` per check, and `dependency.auto_retry` for transient failures.

**Step 4.3 - Dependency Provisioning**
If missing and authorized, create or deploy services, databases, tables, queues, topics, storage resources, and search indexes. Respect `safety.approval_required` for any privileged or production-affecting actions.

**Step 4.4 - Dependency Configuration**
Configure application settings, environment variables, service URLs, API keys, secrets, database connections, credentials, mappings, queue subscriptions, retry policies, workflow definitions, event routing, and trigger configurations.

**Step 4.5 - Dependency Data Initialization**
Initialize prerequisite data: customer types, statuses, regions, product categories, brands, units of measure, RFQ statuses, approval rules, workflow states, transitions, and workflow templates.

**Step 4.6 - Dependency Integration Validation**
Validate API communication, database communication, event communication, workflow orchestration, and cross-module interactions.

**Step 4.7 - Dependency Readiness Certification**
Certify dependencies across all readiness states (see `dependency.readiness_states`): available, configured, initialized, and operational.
Respect `dependency.block_until_certified` - do not proceed to testing if any dependency is not certified.

**Deliverables:**

- Dependency Discovery Report
- Dependency Health Report
- Dependency Provisioning Report
- Dependency Configuration Report
- Dependency Data Initialization Report
- Dependency Integration Validation Report
- Dependency Readiness Certification Report

## 5. Dependency Relationship Analysis

**Objective:** Understand execution order and testing sequence.

**Step 5.1 - Data Dependency Analysis**
Identify parent-child relationships, foreign keys, shared entities, and data ownership.

**Step 5.2 - Service Dependency Analysis**
Identify service call chains, API sequencing, and event sequencing.

**Step 5.3 - Workflow Dependency Analysis**
Identify workflow entry points, workflow states, workflow transitions, and approval chains.

**Step 5.4 - Dependency Matrix Generation**
Build the testing execution order.
Default: `execution_order.default_sequence` (or domain-specific sequence from `business_domain.domain_configs.<domain>.default_execution_sequence`).
If dependency order cannot be fully determined, apply `execution_order.fallback_strategy`.

**Deliverables:**

- Dependency Relationship Matrix

## 6. Integration Testing Plan Generation

**Objective:** Produce the master plan that governs all subsequent testing.

Generate all required elements (see `testing_plan.required_elements`): objectives, scope, assumptions, risks, test strategy, test execution order, validation criteria, and exit criteria.
Apply `testing_plan.default_strategy` when no strategy is specified.
Set minimum coverage threshold per `testing_plan.minimum_coverage_threshold`.

**Deliverables:**

- Integration Testing Master Plan

## 7. Test Asset Preparation

**Objective:** Create realistic test entities that exercise the full dependency chain.

Generate test assets by category (see `test_assets`): customer assets (customers, contacts, addresses), product assets (products, inventory, pricing), supplier assets (suppliers, contracts, certifications), and user assets (administrators, buyers, supplier-users, sales representatives) as applicable.
Use `test_assets.default_entity_count` per type and `test_assets.realistic_data` patterns.

**Deliverables:**

- Test Asset Package

## 8. Test Data Generation and Loading

**Objective:** Populate the environment with data in dependency order.

Generate scripts that create master data, dependent data, and relationship data. Execute loading in dependency order. Validate successful insertion, referential integrity, and relationship creation.

**Deliverables:**

- Test Data Loading Report

## 9. Integration Testing Script Generation

**Objective:** Create executable tests covering the full integration surface.

Generate tests for all configured coverage areas (see `test_coverage`): API operations (CRUD, authentication, authorization, error handling), database operations (inserts, updates, deletes, queries), event operations (publish, consume, retry), and workflow operations (end-to-end execution, state transitions, exception handling).
Apply `test_coverage.default_depth` for coverage level.

**Deliverables:**

- Integration Test Suite

## 10. End-to-End Transaction Testing

**Objective:** Validate complete business workflows across module boundaries.

Execute transaction workflows (see `transaction_workflows` and domain-specific workflows from `business_domain.domain_configs.<domain>.transaction_workflows`):

| Workflow | Steps | Validation Points |
|---|---|---|
| Customer Lifecycle | Create -> Update -> Activate -> Deactivate | entity_created, state_transition_valid, audit_log_recorded |
| Product Lifecycle | Create -> Update -> Price -> Inventory Sync | entity_created, pricing_consistent, inventory_in_sync |
| RFQ Workflow | Submission -> Supplier Matching -> Approval | rfq_created, suppliers_matched, approval_granted |
| Quotation Workflow | Create -> Revise -> Approve -> Accept | quotation_created, revision_tracked, approval_granted, acceptance_recorded |
| Order Workflow | Create -> Allocate -> Ship -> Invoice -> Pay | order_created, inventory_allocated, shipment_dispatched, invoice_generated, payment_received |

**Deliverables:**

- Transaction Validation Report

## 11. Failure and Resilience Testing

**Objective:** Confirm the system degrades gracefully under adverse conditions.

Validate behavior under each configured failure scenario (see `resilience_testing.failure_scenarios`): missing data, invalid data, API failures, database failures, queue failures, authentication failures, service outages, and third-party outages.
Verify expected behavior per scenario (see `resilience_testing.expected_behavior`).

**Deliverables:**

- Failure Analysis Report

## 12. Data Reconciliation and Integrity Validation

**Objective:** Verify data correctness across all systems after testing.

Validate integrity checks (see `reconciliation.integrity_checks`), workflow checks (see `reconciliation.workflow_checks`), and audit checks (see `reconciliation.audit_checks`).
Flag mismatches exceeding `reconciliation.tolerance` thresholds (count, amount, timestamp drift).

**Deliverables:**

- Reconciliation Report

## 13. Final Reporting and Certification

**Objective:** Produce the definitive readiness assessment.

Generate executive summary, scope, status, findings, statistics, coverage analysis, defect analysis (by severity, see `certification.severity_levels`), recommendations, risk assessment, and mitigation plan.

**Final certification status must be one of** (see `certification.statuses`):

| Status | Meaning | Blocking Defects Allowed |
|---|---|---|
| Integration Certified | All integration tests pass; all dependencies validated; no blocking defects | 0 |
| Ready for UAT | Integration passed; minor defects or gaps remain for UAT to cover | 0 |
| Ready for Production | Integration and UAT passed; system meets production criteria | 0 |
| Ready with Conditions | Passing with documented caveats that must be resolved post-release | 0 |
| Not Ready | Blocking defects, missing dependencies, or unresolved infrastructure gaps | Any blocking defect |

**Deliverables:**

- Defect Analysis Report
- Final Integration Testing Certification Report
