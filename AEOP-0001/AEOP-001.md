# AOEP-001 — Community Balancing with Dual-Stage Axie Score Voting Mechanism Proposal

## Preamble

- **AOEP Number:** AOEP-0001  
- **Title:** Community Balancing with Dual-Stage Axie Score Voting Mechanism Proposal
- **Description:** A lightweight governance and experimentation process allowing allowlisted contributors to submit Rune and Charm balance proposals in JSON format, select the top two proposals via Axie Score–weighted voting, trial both during the off-season, and finalize one for official season deployment through a second Axie Score vote.  
- **Author:** Nova/Psylo @Volt3labs
- **Status:** Draft  
- **Type:** Governance / Game Balance Process  
- **Created:** 2026-01-16  

---

## Abstract

This proposal defines a low-resource, off-season balance governance loop for Axie Infinity in which selected contributors submit validated Rune and Charm JSON proposals. The community selects the top two proposals using Axie Score–weighted voting. Both proposals are deployed sequentially during the off-season as live trials. A second Axie Score–weighted vote selects the final winning proposal, which becomes the canonical balance configuration for the next official season.

## Update #1 — Technical Feasibility & Primary Constraint

After discussion with Sky Mavis and community members, there is broad conceptual support for the idea of a community-driven balance council and experimental off-season process.

However, the main blocking issue identified is **technical risk and testing cost**.

Even small numerical balance changes can introduce:
- Client-side bugs (UI, state machines, edge cases)
- Gameplay logic regressions
- Desynchronization between client and server
- Exploits caused by unforeseen interaction effects

Without strong safeguards, each balance iteration would require heavy manual QA and engineering validation, making the process too resource-intensive and therefore unattractive for the core team.

As a result, the first prerequisite for making a low-resource Balance Council viable is:

> Making the game client and server pipeline resilient to frequent balance configuration changes through rigorous, automated, multi-layer testing (data validation, simulation, integration, and regression testing).

Only if balance patches can be deployed safely, repeatedly, and cheaply does a community-driven iteration loop become operationally realistic.

A secondary concern raised by both Sky Mavis and community members is the risk of **unfair market advantage** (information asymmetry, early access to balance knowledge), which must be addressed separately in later sections.

---

## Pre-requisite — Automated Balance Safety Pipeline (Simplified Engineering Plan)

To allow frequent balance changes without breaking the game or requiring heavy manual testing, balance data (runes and charms) must be treated like code: validated, tested, and automatically rejected if unsafe.

### 1. Balance Data Validation

Before anything runs in-game, every balance file must pass simple automatic checks:

- Correct format (JSON schema)
- All required fields exist
- Numbers are within allowed ranges
- All IDs referenced actually exist
- No accidental deletions or duplicated entries
- Diff vs current balance is generated and reviewed

This prevents broken or malformed data from ever reaching the client.

### 2. Automatic Battle Simulation

Run the game logic without graphics (headless mode) using the new balance data:

- Simulate thousands of battles with random teams and seeds
- Ensure battles always finish
- Check that no stat becomes infinite, negative, or NaN
- Ensure no infinite loops or soft locks occur
- Verify that core rules (turn order, energy, cooldowns) stay valid

This catches most gameplay-breaking bugs early.

### 3. Basic Client Smoke Tests

Automatically launch the client with the new balance data and:

- Open main menus
- Open rune/charm screens
- Start and finish a few battles
- Load tooltips and descriptions

The goal is only to ensure “nothing obvious breaks,” not to fully QA the game.

### 4. Simple Exploit Detection

Add automated checks for obvious abuse patterns:

- Unlimited stacking
- Zero-cost or infinite actions
- Extreme damage or healing spikes
- Broken cooldown or energy systems

These can be rule-based at first and improved over time.

### 5. Continuous Integration (CI) Gate

All steps above run automatically whenever a new balance proposal is submitted:

1. Data validation  
2. Battle simulations  
3. Client smoke tests  
4. Exploit checks  

If any step fails, the proposal is rejected and cannot:
- Enter voting
- Be deployed in the off-season

### 6. Versioning and Rollback

- Every balance file has a version number
- Previous versions are kept
- Switching back to a safe version is one click

This makes experimentation safe, fast, and reversible.

This pipeline turns balance changes into low-risk, low-cost operations and is the technical foundation that makes a community-driven balance council realistically possible.

---

## Balance council rationale

Balance decisions in competitive games often lack transparency, experimental rigor, and community legitimacy. This system formalizes balance iteration as a scientific process: hypothesis (proposal), validation, experiment (off-season trial), and conclusion (final vote).

Full DAO-style governance and on-chain voting are considered but can be disregarded if too slow or too complex. Pure Sky Mavis balancing is not possible due to limited resources and insufficient time to properly research, test, and iterate on evolving metas (all resources focused on Atia's Legacy). The dual-stage voting model combines expert filtering with broad participation while ensuring that only empirically tested changes can reach the official season.

The off-season is chosen as the experimentation window because instability is expected and risk is contained. JSON-based parameterization allows numerical tuning without client updates, minimizing engineering overhead and enabling rapid iteration.

---

## Specification

### 1. Roles

- **Allowlisted Proposers:** Submit balance proposals in canonical JSON format. Allowlisted Proposers can be Top players, Analyst, Streamers, anyone recognized for his deep knowledge of Axie Origins game mechanics.
- **Voters:** Axie Score–eligible accounts participating in weighted voting.
- **Operators:** Small internal team responsible for validation, deployment, and vote tallying.

### 2. Proposal Format

Each proposal includes:
- `runes.json` (see runes.json example in the folder)
- `charms.json` (I can also prepare it if you ask me)
- `summary.md` (proposer explains goals, expected impact, risks, evaluation metrics)

All numeric parameters are isolated in a `stats` object to allow tuning without modifying mechanics.

### 3. Validation

A single automated script performs:
- JSON schema validation
- Referential integrity checks
- Change magnitude limits
- Diff generation against the current canonical version

Only validated proposals proceed to voting.

(This is a draft proposal - I have generated a script but did not test it so I won't include here - I can test it and share it here on demand)

### 4. Vote #1 — Top-2 Selection

- Axie Score snapshot taken at vote start.
- Formula can be:
--Weight function: `min(cap, sqrt(AxieScore))`. Square root softens whale dominance - Instead of influence growing linearly (Score = 100 → weight 100), it grows slower: Score 100 → weight 10 & Score 10,000 → weight 100
--Or we can axieScore*axsStaked like governance portal
- Voting method: single-choice or ranked-choice.
- Output: Two proposals with highest weighted totals.

### 5. Off-Season Trial

- Deployment via seasonal configuration switch. No client patch, we just change the JSON file that is called by the client and return by the server
- Schedule:
  - Week 1: Proposal A active.
  - Week 2: Proposal B active.
- Metrics collected from existing telemetry:
  - Win rate
  - Pick rate
  - Meta diversity
  - Exploit detection
  - Player sentiment

### 6. Vote #2 — Final Selection

- Same electorate and weighting rules.
- Options: Proposal A vs Proposal B.
- Winner becomes the official season balance.

### 7. Canonical Release

Winning JSON is versioned and published as the next season’s canonical balance configuration and can be rolled back if critical issues are detected.


## Reference Implementation

A minimal implementation consists of:

1. JSON Schema + validation script  
2. Axie Score snapshot (existing already)
3. Vote tally script with weight function  (can be hosted on Governance portal or on my website)
4. Seasonal config switch for JSON versions  (upload new JSON script)

All components are off-chain and can be implemented within one week by a small team or a single dev.

---

## Security Analysis

Primary risks include:

- **Vote manipulation:** Mitigated through Axie Score snapshotting, weight caps, and eligibility thresholds.
- **Malicious proposals:** Prevented via schema validation, diff review, and change limits.
- **Deployment errors:** Reduced through versioned artifacts and fast rollback.

---

## Economic Analysis

This system improves balance legitimacy and player trust at minimal operational cost. By enabling controlled experimentation and community participation, it can increase long-term retention and competitive integrity while avoiding the overhead of full on-chain governance or complex live-ops tooling. Regular, transparent meta shifts driven by this process can also stimulate economic activity by renewing demand for Axies as players adapt to evolving competitive environments.

## AOEP-001 Diagram

```mermaid
flowchart TD
  A[Allowlisted Proposers<br/>Top players / Top Analysts] --> B[Submit Proposal<br/>runes.json + charms.json + summary.md]
  B --> C[Validation Script<br/>schema + placeholders/stats + safety limits + diff]
  C -->|Valid| D[Vote #1<br/>Axie Score weighted<br/>Select Top 2]
  C -->|Invalid| X[Fix errors<br/>Resubmit]

  D --> E[Off-Season Trial]
  E --> E1[Week 1: Proposal A<br/>Season switch -> A JSON]
  E --> E2[Week 2: Proposal B<br/>Season switch -> B JSON]
  E1 --> F[Telemetry + Metrics]
  E2 --> F

  F --> G[Vote #2<br/>Axie Score weighted<br/>A vs B]
  G --> H[Winner -> Official Season Patch<br/>Publish canonical JSON]
  H --> I[Rollback possible<br/>Re-point season.json]
