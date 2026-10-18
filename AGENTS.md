# Codex Instructions: Decision-First Spec-Driven Development for OCI

This file is the complete, canonical workflow for this repository. Codex automatically loads the root `AGENTS.md` as repository guidance, so all required spec-driven instructions live here.

This repository defines a decision-first, spec-driven development approach for applications and infrastructure built on Oracle Cloud Infrastructure (OCI), using Codex as the coding agent.

## Purpose and scope

Use this workflow for new application capabilities, behavior-changing features, substantial architecture changes, and OCI infrastructure or workload design. It makes important product and technical choices visible before a spec or implementation commits the project to them.

A clearly bounded bug fix, formatting change, documentation correction, or small refactor can follow a direct user request without creating a full requirements/design/tasks set, unless the user asks for spec artifacts. An explicit request to skip the decision checkpoint also allows the requested spec to proceed; record assumptions and unresolved questions rather than silently filling gaps.

## Non-negotiable phase gates

1. Work in this order: Requirements → Design → Tasks → Implementation.
2. Create one decision file for the requested phase before creating that phase’s spec.
3. If the decision file is missing, create it and stop. Ask the user to review and answer it. Never create the decision file and its final spec in the same turn.
4. If the decision file exists but has unanswered or ambiguous items, ask about those items and stop. A suggested or recommended choice is not an answer.
5. Once the user has completed the decision file and asks to proceed, generate only that phase’s document. Do not automatically create decisions or documents for the next phase.
6. Do not start implementation until requirements, design, and tasks are complete and the user explicitly asks for implementation.
7. If the user asks to revise an existing phase, inspect its decisions and related documents first. Update the decision record before changing the spec when the revision changes a material decision.

The user may provide choices in the initial request, but still create the decision file first and stop so they can review the record. If the user explicitly skips the decision checkpoint, follow that instruction and label any remaining assumptions and open questions.

## File layout

Use one directory per feature, application, or workload:

```text
specs/<spec-name>/
  decisions/
    _decisions-requirements.md
    _decisions-design.md
    _decisions-tasks.md
  requirements.md
  design.md
  tasks.md
```

Use a short, stable, lowercase kebab-case `<spec-name>`. Keep decision files as the record of choices and rationale; keep the phase documents as the reviewed specification. Do not put OCI credentials, private keys, or secret values in either location.

## General decision-file rules

- Match the user’s language. Use English only when the language is unclear.
- Tailor questions to the actual domain and request. Avoid generic questionnaires and decisions irrelevant to the work.
- Explain why each decision matters and what it changes in scope, architecture, cost, security, operations, or delivery.
- Offer two to four concrete options, including “Other” when useful. A recommendation may be included with a concise rationale, but never treat it as approved until the user says so.
- Leave a clearly labeled answer field for each question. Mark the file “Awaiting user input” until every required item has an answer or the user explicitly defers it with a reason.
- Handle partial answers by preserving completed answers and asking only for unresolved items.
- For later phases, read the previous phase’s final document and decision file. Reconfirm choices only when the current phase introduces a new decision or changes an earlier one.

### Decision-file template

```markdown
# Decisions: [Requirements | Design | Tasks]

Status: Awaiting user input

> Review each question and fill in its Answer. Recommendations are guidance only.

## [Decision area]

### [Specific decision]

**Why it matters:** [Project-specific impact]

**Question:** [Question requiring a choice]

**Options:**
1. [Option; optionally mark Recommended and explain why]
2. [Option]
3. [Option]
4. Other: [describe]

**Answer:** _Awaiting user input_

---
```

## Requirements phase

**Purpose:** Define what to build and how success will be recognized, without selecting implementation technologies or OCI architecture.

Decision questions can cover:

- Users, actors, and important workflows.
- MVP scope, exclusions, and release boundaries.
- Business rules and data sensitivity or retention needs.
- Observable acceptance conditions and success measures.
- Required performance, availability, accessibility, recovery, or security outcomes.
- Budget, schedule, regulatory, data-residency, integration, and organizational constraints.

The resulting `requirements.md` should state the problem, goals, users, scope and non-goals, functional requirements, measurable acceptance criteria, quality attributes, assumptions, and open questions. Keep requirements solution-neutral where possible. Do not decide on OCI services, languages, frameworks, or network topology in this phase.

## Design phase

**Prerequisite:** The requirements document and its decisions are complete and accepted.

**Purpose:** Define how the approved requirements will be met. Reference the requirements by stable identifiers where practical.

Ask only the OCI and application design questions that apply. Potential decision areas include:

- **Tenancy and access:** tenancy context, compartment boundaries, identity model, workload principals, and least-privilege IAM policies.
- **Location and availability:** target region, service availability, fault-domain or availability-domain strategy where applicable, environment separation, quotas, and data residency.
- **Networking:** VCN and subnet design, public or private exposure, ingress and egress, network security groups, DNS, and hybrid connectivity.
- **Services and compute:** OCI service choices, compute shape families, managed database or storage options, containers such as OKE, or serverless options such as Functions, based on requirements.
- **Data and secrets:** data flows, encryption, key and secret management, backup, retention, and recovery.
- **Security and operations:** audit, logging, monitoring, vulnerability and threat detection, incident ownership, and applicable compliance controls.
- **Resilience and cost:** failure modes, scaling, recovery time and point objectives, major cost drivers, and capacity assumptions.
- **Delivery:** infrastructure-as-code tool and version, module boundaries, state handling, environments, deployment path, and rollback approach.
- **Correctness properties:** whether formal invariants or property-based tests are useful for core domain behavior; include them only when relevant and agreed.

The design should explain the selected architecture, important alternatives, interfaces, data model, security boundaries, operational behavior, assumptions, risks, and unresolved decisions. Do not invent OCIDs, region availability, quotas, rates, shapes, or policy requirements. Verify time-sensitive OCI facts against current Oracle documentation when they affect the design. Treat OCI Landing Zones as reference architectures that may be appropriate for a workload, not as a mandatory choice.

Use OCI’s Well-Architected concerns as a review lens: security and compliance, reliability and resilience, performance and cost optimization, and operational efficiency. Do not claim compliance from a design alone.

## Tasks phase

**Prerequisite:** The design document and its decisions are complete and accepted.

**Purpose:** Turn the approved design into an ordered, reviewable implementation plan.

Decision questions can cover delivery slices, dependencies, migration or compatibility, verification evidence, environment rollout, rollback, ownership, and deployment sequencing. Include OCI provisioning steps only when they are in scope.

The resulting `tasks.md` should use stable task IDs, ordered milestones, dependencies, and a clear definition of done. Each task should point to relevant requirements or design sections and state its expected outcome and verification evidence. Separate writing infrastructure code from applying it to an OCI tenancy. Deployment or destructive operations still require an explicit user request at execution time.

## OCI implementation skills

Before implementing OCI services or infrastructure, consult `.agents/skills/oci/SKILL.md` and follow its routing table to the smallest matching specialist skill. Use that guidance for service-specific implementation, configuration, deployment procedures, and troubleshooting. The upstream collection uses paths such as `oci/oke/...` relative to its source tree; resolve those under `.agents/skills/oci/` in this repository and adjust example shell commands to match. The skill complements this repository's specs and phase gates; it does not itself authorize changes to an OCI tenancy. Follow the explicit deployment request and any skill-specific approval gates before running cloud-mutating commands.

## Implementation phase

Implementation starts only after the three specs are complete and the user explicitly requests it. Use approved task IDs and preserve traceability to requirements and design. If implementation exposes a material conflict or an unanswered choice, stop at that decision, update the relevant decision file, and get the user’s input before proceeding with the affected change.

Do not run Terraform apply, OCI CLI create/update/delete actions, OCI Resource Manager stack apply, or equivalent cloud-changing operations unless the user explicitly asks for that deployment. A request to write or review infrastructure code does not authorize provisioning. Never commit credentials, private keys, or secret values.

## OCI and operational constraints

- Surface OCI-specific choices that matter to the workload, including tenancy and compartments, IAM, regions and availability, networking, service selection, secrets and keys, observability, resilience, cost, and infrastructure-as-code.
- Do not invent tenancy details, OCIDs, service availability, quotas, cost figures, compliance obligations, or performance targets. Ask or mark them unresolved.
- Never place credentials, private keys, or secret values in specs or source control. Refer to the approved secret-management mechanism.
- Preserve the user’s language in decision files and specifications.

## Updating and maintaining specs

- Read all relevant existing decisions and phase documents before changing them.
- Keep answers and rationale intact when they remain valid; do not carry assumptions across phases as if they were approved decisions.
- If a material decision changes, update its decision record and the affected specs so they agree.
- Mark unresolved items as open questions with an owner or needed input where known. Do not hide them in prose as facts.
- Preserve the user’s language and established repository conventions.

## OCI references

Consult current Oracle documentation when specific service behavior, availability, limits, pricing, or security requirements matter:

- [OCI Landing Zones overview](https://docs.oracle.com/en-us/iaas/Content/cloud-adoption-framework/oci-landing-zones-overview.htm)
- [OCI Well-Architected Framework](https://docs.oracle.com/en/solutions/oci-best-practices/toc.htm)
- [IAM tenancy and compartments](https://docs.oracle.com/en-us/iaas/Content/Security/Reference/iam_security_topic-IAM_Tenancy_and_Compartments.htm)
