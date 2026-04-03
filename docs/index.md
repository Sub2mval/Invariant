<!-- docs/index.md -->

---
hide:
  - toc
---

# Project Sentinel

**A deterministic pricing engine for US Government Contract and Civilian Infrastructure solicitations.**

*Built by Invariant. Currently in closed pre-alpha.*

---

## The Problem

A competitive SAM.gov solicitation between $10M and $200M in total contract value arrives as a 300-page PDF. Your senior estimator — your most expensive and least replaceable resource — spends three weeks reading it, extracting a Work Breakdown Structure by hand, cross-referencing GSA schedules, and building a cost basis in a spreadsheet that is not version-controlled and not reproducible.

That is not the cost. That is the symptom.

The cost is the bids you do not submit because your estimating capacity is the ceiling. The proposals that miss the competitive range because the pricing research was incomplete. The contracts awarded at margins that were wrong from the first number entered.

For a mid-market GovCon firm pursuing 8–12 solicitations per year, this is not an operational inconvenience. It is the primary constraint on revenue growth.

---

## What Project Sentinel Does

Sentinel ingests a solicitation document and returns a fully sourced, human-approved financial baseline. It does not generate narrative. It does not summarize. It produces a structured cost artifact — every line item priced, every price traced to a verifiable source, every decision logged — that your proposal team can defend on the day it is submitted.

The pipeline runs four stages:

| Stage | What Happens |
|---|---|
| **Task Decomposer** | Extracts a formal Work Breakdown Structure from the solicitation |
| **HIL Gate** | Your estimator reviews the scope, approves it, and the pipeline continues |
| **Deep Research Swarm** | Parallel agents find the lowest verifiable commercial price for every open line item |
| **Long Context Agent** | Compiles the approved pricing into a locked Bid Cost Report and Excel baseline |

Human review is not optional. It is enforced at the architecture level. No external research runs without an approved scope. No baseline compiles without an approved cost database. Your estimator's judgment stays in the loop at every stage that requires it.

---

## Who This Is For

Sentinel is built for one operating environment:

- **GovCon prime contractors** with $50M–$500M in annual revenue
- Pursuing **FAR Part 15 negotiated acquisitions, IDIQ task orders, and competitive set-aside awards**
- Where the binding constraint on bid volume is **senior estimator capacity, not business development pipeline**

If your win rate is acceptable but the number of qualified bids you submit per year is the ceiling, that is the problem Sentinel is built to remove.

---

## Current Status

Sentinel is in closed pre-alpha. We are working directly with a small number of GovCon firms to validate the engine against live solicitations. Access is not self-serve. There is no public API. There is no general availability date we are prepared to commit to.

What exists today: a working pipeline, real output artifacts, and a direct engagement model with the firms in the current pilot cohort.

What does not exist yet: third-party security certifications, a formal compliance program, or an SDK. We document what is and is not available on the [Security & Trust](security_and_trust.md) page. We recommend reading it before requesting access.

---

## The Commercial Case

A firm that increases qualified bid submissions from 8 to 14 per year — while holding win rate constant at 22% — generates approximately 1.3 additional contract awards annually. At an average contract value of $8M with a 12% fee, that is over $1M in additional gross margin per year.

Sentinel's platform price at general availability is **$15,000/month**.

Pilot participants who proceed to a paid engagement receive preferred pricing. That conversation happens directly, not on this page.

---

## Request Pilot Access

We are accepting a limited number of pilot engagements. If your firm operates in the target market and you have a live solicitation you are willing to run through the engine, that is the basis for a conversation.

<div style="text-align: center; margin: 2.5rem 0;">
  <a href="https://forms.google.com/your-form-link-here" target="_blank" style="background-color: #e65100; color: white; padding: 14px 32px; font-size: 1rem; font-weight: 600; text-decoration: none; border-radius: 4px; letter-spacing: 0.03em;">
    REQUEST PILOT ACCESS →
  </a>
</div>

!!! note ""
    We read every request personally. If the fit is right, we will reach out to schedule a call. If it is not the right time, we will tell you that directly.
