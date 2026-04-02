<!-- docs/getting_started.md -->

# Getting Started

**Modules & Integration Reference**

Project Sentinel is designed for integration into existing GovCon business development and proposal workflows. It exposes a typed API, two purpose-built MCP server modules, and a configuration layer that adapts to your existing ERP and document management infrastructure.

---

## Prerequisites

Before deploying Sentinel, confirm the following:

- [ ] Cloud tenancy or on-premises Kubernetes cluster meeting [infrastructure requirements](architecture.md)
- [ ] Identity provider configured for SAML 2.0 or OIDC federation
- [ ] Client configuration profile completed (NAICS codes, labor category catalog, fee structure, ERP connection string)
- [ ] At least one user provisioned with the `sentinel.estimator` role
- [ ] Network policy configured to allow outbound HTTPS to the [approved external domain allowlist](security_and_trust.md#external-research-agents-stage-c)

---

## Deployment

Sentinel is distributed as a Helm chart. Standard deployment targets AWS EKS, Azure AKS, and GCP GKE. On-premises Kubernetes (1.27+) is supported with a separate configuration profile.
```bash
helm repo add invariant https://charts.invariant.io
helm repo update

helm install sentinel invariant/project-sentinel \
  --namespace sentinel \
  --create-namespace \
  --values ./sentinel-client-config.yaml
```

The client configuration file (`sentinel-client-config.yaml`) is generated during onboarding and contains your NAICS codes, labor category mappings, fee structure, and ERP connection parameters. It is not a public artifact.

---

## Core API Exports

All API endpoints are authenticated via Bearer token (API key issued to a `sentinel.integration` service account). Base URL is the internal cluster endpoint exposed via your ingress controller.

### `POST /v1/pipeline/run`

Initiate a new pipeline run against an uploaded RFP document.
```http
POST /v1/pipeline/run
Authorization: Bearer {api_key}
Content-Type: multipart/form-data

rfp_document: [binary]
opportunity_id: "OPP-2025-0047"
override_config: {}
```

**Response:**
```json
{
  "run_id": "sntnl_run_a1b2c3d4",
  "status": "stage_a_processing",
  "opportunity_id": "OPP-2025-0047",
  "created_at": "2025-09-12T14:32:00Z",
  "hil_review_url": "https://your-sentinel-instance/review/sntnl_run_a1b2c3d4"
}
```

---

### `GET /v1/pipeline/{run_id}/status`

Poll pipeline status. Poll interval should not exceed 1 request per 30 seconds.
```json
{
  "run_id": "sntnl_run_a1b2c3d4",
  "status": "awaiting_hil_approval",
  "stage": "B",
  "open_items": 23,
  "covered_items": 41,
  "hil_assigned_to": "j.smith@yourcompany.com",
  "hil_deadline": "2025-09-14T14:32:00Z"
}
```

**Status values:** `stage_a_processing` → `awaiting_hil_approval` → `stage_c_research` → `stage_d_compiling` → `complete` → `failed`

---

### `GET /v1/pipeline/{run_id}/baseline`

Retrieve the compiled financial baseline after the run reaches `complete` status.
```json
{
  "run_id": "sntnl_run_a1b2c3d4",
  "opportunity_id": "OPP-2025-0047",
  "baseline_locked_at": "2025-09-13T09:14:22Z",
  "total_direct_cost": 4218750.00,
  "total_loaded_cost": 5320000.00,
  "line_items": 64,
  "unresolved_items": 1,
  "exports": {
    "xlsx": "/v1/pipeline/sntnl_run_a1b2c3d4/export/xlsx",
    "json": "/v1/pipeline/sntnl_run_a1b2c3d4/export/json",
    "sql_push_status": "delivered"
  }
}
```

---

### `GET /v1/pipeline/{run_id}/audit`

Retrieve the full audit log for a completed run, including HIL approval record and all source citations.
```json
{
  "run_id": "sntnl_run_a1b2c3d4",
  "hil_approval": {
    "approved_by": "j.smith@yourcompany.com",
    "approved_at": "2025-09-12T18:44:01Z",
    "manifest_hash": "sha256:e3b0c44298fc1c149afb..."
  },
  "line_items": [
    {
      "wbs_id": "1.2.3",
      "title": "Field Technician II – Year 1",
      "price_selected": 87.50,
      "price_unit": "per hour",
      "source": "GSA MAS Schedule 70, Contract GS-35F-0119J",
      "source_url": "https://www.gsaadvantage.gov/...",
      "retrieved_at": "2025-09-12T20:11:44Z",
      "competing_prices_considered": 3
    }
  ]
}
```

---

## Module 1 — SAM.gov Sourcing MCP Server

**Function:** Continuously retrieves and scores active SAM.gov solicitations against the client's configured capacity profile.

The Sourcing module runs as a persistent MCP server process. It polls SAM.gov at a configurable interval, filters solicitations by NAICS code, set-aside type, place of performance, and contract value range, and scores each opportunity against the client's current pipeline load and past performance record.

It does not bid on anything. It does not send notifications. It writes to a structured opportunity queue that your BD team reviews.
```yaml
sourcing_module:
  poll_interval_hours: 6
  naics_codes: ["541330", "541519", "237310"]
  set_aside_types: ["8A", "SDVOSB", "WOSB", "FULL_AND_OPEN"]
  contract_value_min: 5000000
  contract_value_max: 200000000
  place_of_performance_states: ["VA", "MD", "DC", "TX", "CA"]
  scoring_weights:
    past_performance_match: 0.35
    naics_primary_match: 0.30
    capacity_headroom: 0.20
    incumbent_signal: 0.15
```

**Output:** Structured opportunity records pushed to the `sentinel.opportunities` table in your ERP or a standalone PostgreSQL schema.

---

## Module 2 — Traceability & DeepResearch Logs

**Function:** Provides a structured audit trail of every constraint evaluated and every pricing decision made during a pipeline run.

Every Stage C agent writes a research log record for each line item it prices. The Traceability module indexes these records and exposes them via API and a read-only UI component that can be embedded in your proposal management system.

Each traceability record contains:

- The specification string used to query external sources
- All sources queried and prices found
- The selection rationale (lowest verifiable price meeting spec)
- The constraint check result (met / violated / unresolvable)
- The agent run ID and timestamp

This log is the artifact your pricing analyst attaches to the cost volume as substantiation. It is not a summary. It is a machine-generated evidence record.
```bash
# Retrieve traceability log for a specific line item
GET /v1/pipeline/{run_id}/trace/{wbs_id}

# Export full DeepResearch log as PDF attachment
GET /v1/pipeline/{run_id}/trace/export/pdf
```

---

## SDK

A Python SDK is available for teams integrating Sentinel into existing proposal automation workflows.
```bash
pip install sentinel-sdk
```
```python
from sentinel import SentinelClient

client = SentinelClient(
    base_url="https://sentinel.yourcompany.internal",
    api_key="sk_live_..."
)

# Initiate a pipeline run
run = client.pipeline.run(
    rfp_path="./solicitations/W912DY-25-R-0004.pdf",
    opportunity_id="OPP-2025-0047"
)

# Poll until HIL approval is required
run.wait_for_stage("awaiting_hil_approval")
print(f"HIL review required: {run.hil_review_url}")

# After approval, wait for completion and retrieve baseline
run.wait_for_completion()
baseline = run.get_baseline()
print(f"Total loaded cost: ${baseline.total_loaded_cost:,.2f}")
```

---

!!! question "Ready to run your first solicitation?"
    Contact your Invariant solutions engineer or [request pilot access](index.md#pilot-access) to receive your client configuration file and deployment credentials.
