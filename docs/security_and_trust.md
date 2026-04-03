<!-- docs/security_and_trust.md -->

# Security & Trust

**Project Sentinel is in pre-alpha.** The architecture described here reflects the intended production design. Where something is not yet implemented, we say so directly.

---

## Human-in-the-Loop: Why It Exists

The HIL gates in the Sentinel pipeline are not a UX feature. They are a structural response to two facts about GovCon pricing work that do not change regardless of how good the underlying models get.

**First: automated scope extraction makes mistakes.** A 300-page solicitation with cross-referenced attachments, ambiguous labor category definitions, and agency-specific interpretations of FAR clauses will produce extraction errors. The HIL gate at the Execution Plan stage exists so a human with domain knowledge catches those errors before they are priced and compiled into a baseline your firm submits under its name.

**Second: pricing judgment cannot be fully encoded.** The lowest verifiable commercial price is not always the right price. Incumbent relationships, teaming commitments, and price-to-win strategy are context that lives in your estimator's head, not in a GSA schedule. The HIL gate at the Cost of Contract DB stage exists so that context can be applied before the baseline is locked.

Every HIL approval is timestamped and identity-linked. The approval record is part of the audit log for that pipeline run. If you are ever asked to substantiate a pricing decision, that record is your evidence of human review.

---

## Data Handling: Current State

At the pre-alpha stage, here is what is true:

- RFP documents you submit are used only to run your pipeline. They are not used to train models, shared with other clients, or retained beyond the scope of your engagement.
- Pricing research conducted by the Deep Research Swarm queries publicly available sources — GSA, SAM.gov, FPDS-NG, and approved commercial sources. No proprietary client data is transmitted to those sources.
- The Cost of Contract DB for your runs is isolated to your engagement. No cross-client data bleed.

We are a pre-alpha product. We do not yet have a formal data processing agreement, a published privacy policy, or third-party security certifications. If your firm requires those before engaging, we are not the right fit at this stage — and we would rather tell you that now than after you have invested time in a pilot conversation.

---

## What We Can Back Up

- **HIL architecture** — implemented and enforced at the pipeline level. Stage C does not execute without a recorded Stage B approval.
- **Source citation on every price** — every line item in the output includes the source URL, retrieval timestamp, and contract vehicle number where applicable.
- **Read-only external research** — Deep Research agents make outbound read requests only. They do not submit forms, create accounts, or interact with external systems beyond retrieval.
- **Audit log per pipeline run** — every run produces a full log of agent actions, HIL approvals, and pricing decisions. You receive this log as part of your output artifacts.

---

## What We Cannot Back Up Yet

- Formal compliance certifications (SOC 2, FedRAMP, CMMC)
- A signed data processing agreement
- A published SLA
- On-premises or private cloud deployment

These are on the roadmap. They are not available today. If your procurement or legal team requires any of them as a condition of engagement, the honest answer is that we cannot meet that bar yet.

---

## Questions

If you have specific security or data handling questions before requesting a pilot, include them in the access request form. We will answer them directly.

<div style="text-align: center; margin: 2.5rem 0;">
  <a href="https://docs.google.com/forms/d/e/1FAIpQLSe9BzXjzuqd1-kILxavX0NV8PTgovWpwyVpSMWLl1d7-vhVlw/viewform?usp=publish-editor" target="_blank" style="background-color: #e65100; color: white; padding: 14px 32px; font-size: 1rem; font-weight: 600; text-decoration: none; border-radius: 4px; letter-spacing: 0.03em;">
    REQUEST PILOT ACCESS →
  </a>
</div>
