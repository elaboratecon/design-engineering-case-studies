# Two Ways to Perform an Action

## TL;DR

Admin users needed a consistent way to make funding decisions across several different workflows—both for individual proposals and in bulk—without introducing errors, UI inconsistency, or technical debt. I designed a unified system that supports both use cases using shared primitives (in this case, React components), backend-driven workflow rules, and only showing complexity when it’s actually needed (progressive disclosure).



## Context

The primary platform I’ve helped build and maintain at NASA JPL is a funding portal that allows researchers to submit proposals in hopes of securing internal funding.

There are multiple funding programs, each with their own workflow. Some programs are simple, single-phase processes that move quickly. Others are complex, multi-phase workflows involving subject matter expert (SME) peer reviews (blind or double-blind), standing review board (SRB) ratings, and division or directorate prioritization and feedback.

Once the review period closes, administrators review all feedback and decide whether to fund the proposal. In special cases, they may withdraw a submission or request revisions from the principal investigator (PI).



## The Problem

Over time, two major issues emerged:

1. **Fragmentation and technical debt**  
   Each funding program had its own custom “Decision Panel” for administrators. This led to duplicated logic, inconsistent behavior, and growing technical debt.

2. **Lack of support for batch actions**  
   Administrators increasingly needed to process multiple proposals at once — but the system only supported single-item decisions.

We needed to:

- Standardize decision-making into a single reusable system.
- Support both individual and batch workflows.
- Prevent errors in a high-stakes, stateful process.



## Key Insight

I approached this as a **system-level design problem**, defining small reusable building blocks and clear backend rules that could support multiple experiences without duplicating logic or diverging over time.

Rather than designing two separate systems, I designed **one underlying set of workflow rules and reusable components** that could be used in two different interaction patterns:

- **Individual Review** via a Decision Panel (modal)
- **Batch Review** via a multi-pane Batch Processing View

Different interfaces; same underlying logic.

This structure also makes it easy to support future workflows by extending backend rules and reusing existing components, instead of creating new bespoke UIs each time.

A secondary goal was improving the developer experience by reducing duplicated UI logic and centralizing workflow rules in the backend.



## Design Goals

- Support both precision (single proposal) and efficient (batch) processes.
- Guide users through complex workflows without overwhelming them.
- Prevent invalid or premature actions.
- Reduce technical debt through shared components and a consistent set of backend rules.
- Make it easy to support new workflows in the future.



## Solution

### 1. Shared Capabilities

The following actions were standardized into reusable components used in both interfaces:

- Update proposal status  
- Assign or update award budgets (when applicable)  
- Send optional messages to the principal investigator  

These became the building blocks for both interaction modes.



### 2. Edge Cases

Some actions were intentionally excluded from batch workflows to reduce risk and cognitive load:

- Withdraw proposal  
- Request revision  

These remain available only in the Individual Review flow.



### 3. Individual Review — The Decision Panel

For single proposals, the Decision Panel presents the workflow as a vertical sequence of steps:

- Steps are ordered top-to-bottom.
- Downstream steps are disabled (greyed out) until they become applicable.
- Available actions are driven by backend-provided workflow rules.

This ensures that:

- The user always understands where they are in the process.
- Only valid actions are selectable.
- The system presents only relevant (valid) choices at each step.



### 4. Batch Review — The Batch Processing View

Batch processing is represented as stacked “panes,” each representing a workflow stage:

- **Submitted** (Inbox icon)  
  Select proposals to stage for processing.

- **Pending** (Unlocked lock icon)  
  Update statuses, assign awards, and compose optional messages.

- **Final Decisions** (Locked lock icon)  
  Finalized, read-only list.

Each pane uses sortable tables, minimal necessary data, and consistent interaction patterns to support efficient bulk operations while minimizing error risk.



## Usability Principles Applied

### Visibility of System Status

- Users are led through explicit workflow paths.
- Disabled states visually indicate unavailable actions.
- Word counters prevent overlong messages.

### Match Between System and Real World / Consistency

- Icons (Inbox, Lock, Save) visually reinforce meaning.
- Both interfaces share design language, components, and workflows.

### User Control and Error Prevention

- Cancel resets changes to last saved state.
- Clearing dependent fields prevents erroneous (contradictory) input.

### Aesthetic and Minimal Design

- Rarely used fields are hidden by default and only shown when the user needs them.
- Each pane contains only the information needed for that step.

### Help and Documentation

- Inline guidance is provided where needed.



## Outcome

This approach reduced technical debt by consolidating multiple bespoke components into a single system, improved usability for both precision and high-volume workflows, and reduced confusion around proposal state transitions. Administrators were able to process proposals more quickly and with greater confidence in system correctness.

---

## Why A Whitepaper?

Because the work I’ve done over the past several years is on sensitive government systems that cannot be publicly shown, I use written case studies like this to explain my design and engineering process — how I think, what tradeoffs I consider, and how I approach complex usability challenges.
