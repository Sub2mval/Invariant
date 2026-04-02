<!-- docs/security_and_trust.md -->

# Security & Trust

**Project Sentinel is deployed in regulated procurement environments.** The architecture reflects the compliance requirements of FAR-covered contractors, not the convenience requirements of a SaaS vendor.

---

## Human-in-the-Loop (HIL): Design Rationale

The HIL checkpoint in Stage B is not a feature. It is a structural constraint derived from two non-negotiable operational requirements:

**1. Estimator accountability cannot be delegated to a system.**

Under FAR 15.404-1, the contractor is responsible for the accuracy and completeness of its cost or pricing data. An automated system that generates a cost baseline without documented human review creates an unacceptable audit exposure. The HIL checkpoint produces a timestamped, identity-linked approval record that constitutes evidence of human review at the scope-definition stage.

**2. Scope errors compound through the pipeline.**

If Stage A misclassifies a labor category or misreads a cross-referenced attachment, and Stage C prices to that misclassification, the resulting baseline is wrong in a way that is structurally invisible—the numbers are internally consistent but externally incorrect. The HIL gate is the only point in the pipeline where a human with domain knowledge can catch a structural error before it is priced and compiled.

### What the Estimator Reviews at the HIL Gate

| Item | Estimator Action |
|---|---|
| WBS nodes with no internal inventory match | Confirm categorization or reassign |
| Labor categories flagged as ambiguous | Map to correct internal LCAT |
| PWS references to unattached documents | Provide the document or mark as unresolvable |
| Scope items that exceed authorized NAICS coverage | Flag for teaming or exclusion |
| Override of any automatically-matched internal item | Optional; logged with justification field |

The approval is recorded as a signed manifest (HMAC-SHA256, keyed to the user's API credential). The manifest is immutable after signing. Any modification to the WBS after signing requires a full pipeline re-run and a new HIL review.

---

## Data Handling

### Document Ingestion

RFP documents are processed within the client's designated compute environment. Invariant does not operate a shared document processing cluster. The ingestion service is deployed as a containerized workload inside the client's cloud tenancy (AWS, Azure, or GCP) or on-premises infrastructure.

**Documents are not transmitted to Invariant infrastructure at any point.**

### Intermediate Data

Pipeline intermediate state (WBS JSON, constraint manifests, agent research logs) is stored within the client's environment in an encrypted-at-rest object store. Sentinel uses AES-256 encryption for all stored artifacts. Encryption keys are managed by the client's KMS—Invariant does not hold or escrow keys.

### External Research Agents (Stage C)

Stage C agents make outbound requests to public data sources (GSA, SAM.gov, FPDS-NG, and approved commercial domains). These requests are read-only and do not transmit any proprietary client data. The query structure sent to external sources contains only the specification parameters for the line item being researched—never the RFP document content, company name, or opportunity identifier.

The allowlist of external domains is defined in the client's configuration file and is enforced at the network policy level, not the application level.

### Audit Log Retention

All pipeline run logs, HIL approval records, and source citation chains are retained for 7 years by default, consistent with FAR 4.703 contract file retention requirements. Retention period is configurable. Logs are stored in the client's environment and are not accessible to Invariant.

---

## Access Control

Sentinel uses role-based access control with four defined roles:

| Role | Permissions |
|---|---|
| `sentinel.admin` | Full configuration access, user management, API key issuance |
| `sentinel.estimator` | HIL review and approval, WBS override, pipeline initiation |
| `sentinel.analyst` | Read-only access to pipeline outputs and audit logs |
| `sentinel.integration` | API-only service account for ERP/SQL push operations |

All authentication is federated through the client's identity provider (SAML 2.0 or OIDC). Sentinel does not maintain its own user credential store.

---

## Compliance Posture

| Framework | Status |
|---|---|
| FAR 52.204-21 (Basic Safeguarding of Covered Contractor Information Systems) | Compliant by architecture |
| DFARS 252.204-7012 (Safeguarding Covered Defense Information) | Configuration guide provided; client attestation required |
| NIST SP 800-171 (CUI handling) | Control mapping available on request |
| SOC 2 Type II | In progress; estimated completion Q3 2025 |
| FedRAMP | Not applicable (client-deployed architecture; no shared cloud service) |

!!! info "CMMC Note"
    Because Sentinel is deployed within the client's environment and does not process CUI on shared Invariant infrastructure, it does not introduce a new CMMC assessment scope boundary. Confirm with your CMMC Registered Practitioner Organization before assertion.

---

## Vulnerability Disclosure

Invariant maintains a responsible disclosure program. Security researchers may submit findings to `security@invariant.io`. We commit to acknowledging receipt within 2 business days and providing a remediation timeline within 10 business days for confirmed vulnerabilities.
