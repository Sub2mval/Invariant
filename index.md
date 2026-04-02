<!-- docs/index.md -->

---
hide:
  - toc
---

# Project Sentinel

**Deterministic pricing infrastructure for GovCon and Civilian Infrastructure contractors.**

*Built by Invariant. Deployed at the proposal desk.*

---

## The $150,000 Problem Sitting in Your Estimating Queue

Every time your team receives a SAM.gov solicitation between $10M and $200M in total contract value, you are committing one of your most expensive resources—a senior estimator at $90–$140k/year fully-loaded cost—to a process that has not changed since 2004.

They open a 300-page PDF. They manually read Section L, Section M, and the PWS. They extract a Work Breakdown Structure by hand. They cross-reference GSA schedules, check incumbent pricing where available, and build a cost basis in a spreadsheet that is not version-controlled, not auditable, and not reproducible.

That process takes **three weeks per RFP**.

For a company pursuing 8–12 solicitations per year, that is **24 weeks of senior estimator capacity** consumed by document extraction and commercial price research. That is not strategy. That is transcription.

The downstream cost is not the labor hours. It is the **bids you do not submit**, the **proposals that miss the competitive range**, and the **contracts awarded at margins that destroy the program before it starts**.

---

## What Project Sentinel Does

Project Sentinel is a **multi-agent pricing engine** that processes a SAM.gov RFP and returns a cross-referenced, fully auditable financial baseline—without hallucination, without opaque inference, and without replacing your estimator's judgment.

It does not generate narrative. It does not summarize documents. It produces a **structured, traceable cost artifact** that your proposal team can defend in a DCAA audit on day one.

The engine runs four deterministic stages:

| Stage | Function | Output |
|---|---|---|
| **Task Decomposer** | Ingests the RFP and extracts a formal, leveled WBS against the SOW | Structured WBS in JSON |
| **HIL Checkpoint** | Maps extracted line items to internal inventory and pauses for estimator approval before external research begins | Approved constraint manifest |
| **Deep Research Fan-Out** | Spins parallel MCP agents to retrieve the lowest verifiable commercial price for every open line item | Price-substantiated line items with source citations |
| **Compiler** | Aggregates all findings into a locked, cross-referenced financial baseline | `.xlsx` / SQL push-ready output |

Every price chosen is traceable to a source. Every constraint met or violated is logged. The output is a baseline—not a guess.

---

## Who This Is For

Project Sentinel is not a general-purpose procurement tool. It is scoped to a specific operating environment:

- **Company size:** $50M–$500M in annual revenue
- **Contract vehicle:** FAR Part 15 negotiated acquisitions, IDIQ task orders, and competitive 8(a)/SDVOSB awards
- **Solicitation complexity:** RFPs with 150–500 pages, multiple CLINs, and mixed labor/ODC structures
- **Internal condition:** The company has at least one senior estimator whose time is the binding constraint on bid volume

If your win rate is acceptable but your **bid volume is the ceiling**, Sentinel removes the ceiling.

---

## The Commercial Case

A mid-market GovCon firm that increases qualified bid submissions from 8 to 14 per year—while holding win rate constant at 22%—generates approximately **1.3 additional contract awards annually**.

At an average contract value of $8M with a 12% fee, that is **$1.04M in additional gross margin per year**.

Sentinel's platform license is **$15,000/month**.

The payback period on the first incremental win is measured in weeks, not quarters.

---

## Pilot Access

Sentinel is currently in a closed pilot program with select F.inc partners and GovCon prime contractors.

Pilot participants receive:

- Full platform access for 90 days
- Onboarding with a dedicated Invariant solutions engineer
- Processing of up to 3 live solicitations with full audit output
- Direct input into the product roadmap

<div style="text-align: center; margin: 2.5rem 0;">
  <a href="https://forms.google.com/your-form-link-here" target="_blank" style="background-color: #e65100; color: white; padding: 14px 32px; font-size: 1rem; font-weight: 600; text-decoration: none; border-radius: 4px; letter-spacing: 0.03em;">
    REQUEST PILOT ACCESS →
  </a>
</div>

---

!!! note "Compliance Note"
    Project Sentinel does not store RFP documents on shared infrastructure. All document ingestion occurs within your designated compute environment. See [Security & Trust](security_and_trust.md) for the full data handling specification.
