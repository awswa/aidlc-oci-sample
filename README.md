# OCI AIDLC Baseline

This repository provides a decision-first, spec-driven workflow for workloads built on Oracle Cloud Infrastructure (OCI). Use it to standardize requirements, architecture, planning, and implementation across application, platform, and infrastructure work.

[AGENTS.md](AGENTS.md) is the canonical workflow and guardrail reference. The OCI skill collection in [`.agents/skills/oci/`](.agents/skills/oci/SKILL.md) provides service-specific guidance when OCI implementation work begins.

## Start a workload

Open the repository in Codex and describe the workload you want to build or change. For a new workload, use a prompt like this:

```text
I want to build [describe the workload]. Read AGENTS.md and follow its spec-driven workflow. Inspect the repository and begin with the Requirements decision checkpoint only. Create only the requirements decision file for this workload, then stop for my review. Do not generate the requirements specification or write implementation code yet. Ask about material unknowns instead of inventing workload details.
```

## Work through the phases

The workflow proceeds through Requirements, Design, Tasks, and Implementation. Each specification phase has a decision file that must be reviewed before its corresponding specification is written. Complete and review one phase at a time, as described in `AGENTS.md`.

Specifications live under a workload-specific directory:

```text
specs/<workload-name>/
  decisions/
    _decisions-requirements.md
    _decisions-design.md
    _decisions-tasks.md
  requirements.md
  design.md
  tasks.md
```

Request implementation explicitly after the requirements, design, and tasks are complete and accepted. Writing infrastructure code does not authorize changes to an OCI tenancy; deployment requires a separate explicit request.

## OCI implementation skills

The Oracle OCI skill collection is included in [`.agents/skills/oci/`](.agents/skills/oci/SKILL.md). Start with its routing table and use the smallest matching specialist skill for service-specific implementation or troubleshooting. Paths in upstream skill text such as `oci/functions/...` refer to files under `.agents/skills/oci/` in this repository.

The collection is sourced from [Oracle Skills for OCI](https://github.com/oracle/skills/tree/main/oci), and its license is included at [`.agents/skills/oci/LICENSE.txt`](.agents/skills/oci/LICENSE.txt). Using a skill does not authorize cloud changes. Follow the deployment and approval rules in `AGENTS.md` before running commands that create or modify OCI resources.
