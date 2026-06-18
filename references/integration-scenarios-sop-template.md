# Continuous Integration Scenarios SOP — Template

> **How to use this template.** This Standard Operating Procedure (SOP) tells the
> Autonomous Integration Testing Specialist *what* to test, *in what order*, *against
> which environment*, and *what "done" means* for your project. Copy this file, fill in
> every `<placeholder>`, delete the guidance blockquotes, and provide the completed
> document when invoking the skill. Anything left blank is treated as `assumed` and
> reported as a gap. Keep the section headings — the skill maps them to its phases and
> `config/skill-config.yaml` keys.

---

## 1. Document Control

| Field | Value |
|---|---|
| SOP title | `<e.g. Order Management CI Integration SOP>` |
| Version | `<e.g. 1.0.0>` |
| Owner / contact | `<name, email>` |
| Last updated | `<YYYY-MM-DD>` |
| Business domain | `<ecommerce | logistics | finance | healthcare | iot | generic>` |
| Target environment | `<dev | staging | qa | pre-prod>` (never `production` without explicit approval) |
| Approval status | `<draft | approved>` |

## 2. Purpose and Scope

> One paragraph: what this SOP certifies and why now (release, regression, new module).

- **In scope:** `<modules / services / workflows under test>`
- **Out of scope:** `<explicitly excluded areas>`
- **System(s) under test:** `<target module(s) and the platform they depend on>`

## 3. Environment and Access

| Item | Value / source |
|---|---|
| Environment target | `<env name>` |
| Base URLs / endpoints | `<api gateway, service URLs>` |
| Credential source | `<approved secret store — NOT inline values>` |
| Required env vars | `<list of names only>` |
| Data stores | `<postgresql / dynamodb / redis / opensearch / ...>` |
| Messaging / events | `<message queue / event bus / topics>` |
| Access constraints | `<VPN, IP allowlist, role required>` |
| Provisioning policy | `<auto-provision when safe | manual approval required>` |

> List **names and sources only** — never paste secrets, tokens, or connection strings.

## 4. Dependency Readiness Requirements

> Each dependency must reach all four readiness states before testing begins:
> `available -> configured -> initialized -> operational`.

| Dependency | Type (internal / infra / external) | Health check | Required readiness | Owner |
|---|---|---|---|---|
| `<customer_management>` | internal | `<GET /health>` | operational | `<team>` |
| `<postgresql>` | infrastructure | `<connection + migration check>` | initialized | `<team>` |
| `<payment_gateway>` | external | `<sandbox ping>` | operational | `<vendor>` |

## 5. Test Data Requirements

| Asset type | Count | Notes / constraints |
|---|---|---|
| `<customers>` | `<5>` | `<realistic, region = US/EU>` |
| `<products>` | `<5>` | `<with pricing + inventory>` |
| `<suppliers>` | `<3>` | `<with contracts>` |
| `<users / roles>` | `<as needed>` | `<admin, buyer, sales rep>` |

- **Load order:** master data → dependent data → relationship data (dependency order).
- **Data source:** `<generate realistic | restore fixture set | anonymized snapshot>`.

## 6. Execution Order

> Override the skill default only if your dependency graph differs. State the reason.

```text
<Foundation -> Master Data -> Customer -> Product -> Pricing -> RFQ -> Quote -> Order -> Fulfillment -> Billing>
```

**Reason for any deviation from the default sequence:** `<...>`

## 7. Integration Scenarios

> The core of this SOP. One block per scenario. Add as many as needed. Priority drives
> execution when time is limited (P1 = must pass to certify).

### Scenario template

| Field | Value |
|---|---|
| **ID** | `<INT-001>` |
| **Name** | `<Order created from accepted quotation>` |
| **Priority** | `<P1 | P2 | P3>` |
| **Type** | `<API | database | event | workflow | end-to-end>` |
| **CI trigger** | `<on pull request | nightly | pre-release | manual>` |
| **Preconditions** | `<customer + product + accepted quotation exist>` |
| **Dependencies** | `<order_management, inventory_management, pricing_engine>` |
| **Test data** | `<which fixtures / generated entities>` |
| **Steps** | `<1. POST /orders from quote  2. Allocate inventory  3. ...>` |
| **Expected behavior** | `<201 Created; order in ALLOCATED state; inventory decremented>` |
| **Validation points** | `<order_created, inventory_allocated, audit_log_recorded>` |
| **Cross-system checks** | `<order total == quote total within tolerance>` |

### Example (filled in)

| Field | Value |
|---|---|
| **ID** | INT-001 |
| **Name** | Order lifecycle: create → allocate → ship → invoice → pay |
| **Priority** | P1 |
| **Type** | end-to-end |
| **CI trigger** | pre-release |
| **Preconditions** | Active customer, in-stock product, valid pricing |
| **Dependencies** | order_management, inventory_management, pricing_engine, billing |
| **Test data** | 1 customer, 1 product (qty ≥ 10), 1 sales-rep user |
| **Steps** | 1. Create order. 2. Allocate inventory. 3. Ship. 4. Generate invoice. 5. Record payment. |
| **Expected behavior** | Each step returns success; order advances Created → Allocated → Shipped → Invoiced → Paid |
| **Validation points** | order_created, inventory_allocated, shipment_dispatched, invoice_generated, payment_received |
| **Cross-system checks** | Invoice amount == order total; inventory decremented by ordered qty; audit log has 5 transitions |

## 8. Failure and Resilience Scenarios

| Scenario | Injected fault | Expected behavior |
|---|---|---|
| `<missing_data>` | `<request unknown customer id>` | `<404 with context>` |
| `<invalid_data>` | `<negative quantity>` | `<400 with validation details>` |
| `<api_failures>` | `<downstream 500>` | `<circuit breaker or retry with fallback>` |
| `<database_failures>` | `<connection drop>` | `<graceful degradation or retry>` |
| `<third_party_outages>` | `<payment gateway timeout>` | `<degrade gracefully or queue for retry>` |

## 9. Data Reconciliation Checks

| Check | Rule | Tolerance |
|---|---|---|
| Referential integrity | `<no orphaned order lines>` | `<0>` |
| Cross-system consistency | `<order total == invoice total>` | `<amount: 0.01>` |
| Count consistency | `<orders created == orders persisted>` | `<0>` |
| Timestamp drift | `<event time vs. record time>` | `<5 seconds>` |
| Audit completeness | `<every state transition logged>` | `<0 missing>` |

## 10. Entry and Exit Criteria

**Entry criteria (testing may begin when):**
- `<environment validated; all P1 dependencies operational; test data loaded>`

**Exit criteria (certification may be issued when):**
- `<all P1 scenarios pass; coverage ≥ 80%; no blocking defects; reconciliation clean>`

## 11. CI Trigger and Cadence

| Trigger | Scope run | Required to pass |
|---|---|---|
| On pull request | `<P1 API + smoke workflows>` | `<yes — blocks merge>` |
| Nightly | `<P1 + P2 end-to-end>` | `<report only>` |
| Pre-release | `<full suite + resilience + reconciliation>` | `<yes — blocks release>` |

## 12. Reporting and Certification Expectations

- **Report format:** `<markdown | json | html>`
- **Required certification decision:** one of `Integration Certified`, `Ready for UAT`,
  `Ready for Production`, `Ready with Conditions`, `Not Ready`.
- **Distribution:** `<who receives the certification report>`

## 13. Sign-off

| Role | Name | Date | Decision |
|---|---|---|---|
| Test owner | `<...>` | `<...>` | `<...>` |
| Release manager | `<...>` | `<...>` | `<...>` |
