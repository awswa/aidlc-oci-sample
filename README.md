# Start Building with Codex

Use this README as the developer entry point for starting application development in this repository. Codex should use [AGENTS.md](AGENTS.md) for the required spec-driven workflow and [usecase.md](usecase.md) for the customer’s use case and requirements. This README explains how to trigger and continue that workflow; it does not replace either source.

## Start the development workflow

Open this repository in Codex and send this prompt:

```text
Build the application based on /Users/janghan/dev/aidlc-decision-oci/usecase.md and /Users/janghan/dev/aidlc-decision-oci/README.md. Read /Users/janghan/dev/aidlc-decision-oci/AGENTS.md and follow its spec-driven workflow. Read usecase.md completely, inspect the existing repository, and begin with the requirements decision checkpoint only. Create only the requirements decision file required by AGENTS.md, then stop and ask me to review it. Do not generate requirements.md or write application code yet. Preserve the customer's stated requirements and ask only about material unknowns or conflicts.
```

Codex loads `AGENTS.md` as repository guidance. The prompt names all three files so the application request, customer context, and workflow are explicit.

## Continue phase by phase

After Codex creates the requirements decision file, review it and fill in the answers. Then ask Codex to generate the requirements document:

```text
I completed the requirements decisions. Generate requirements.md using my answers and usecase.md, following AGENTS.md. Stop after the requirements phase.
```

After reviewing and approving `requirements.md`, ask Codex to start the design phase. It should create the design decision file and stop for your input. Once you complete that file, ask it to generate `design.md`. Repeat the same sequence for the tasks phase and `tasks.md`.

The expected spec layout is:

```text
specs/<application-name>/
  decisions/
    _decisions-requirements.md
    _decisions-design.md
    _decisions-tasks.md
  requirements.md
  design.md
  tasks.md
```

Complete one phase at a time. A decision file and its corresponding final spec are separate steps, as defined in `AGENTS.md`.

## Request implementation

Once the requirements, design, and tasks are complete and reviewed, request implementation explicitly. For example:

```text
Implement the approved task IDs [list IDs] from specs/<application-name>/tasks.md. Follow AGENTS.md and usecase.md. Run the verification steps listed for these tasks and report the results. Do not deploy or make changes in OCI.
```

Codex should keep implementation traceable to the approved specs. Writing application or infrastructure code does not authorize deploying it to OCI; request deployment separately if needed.

## OCI implementation skills

The Oracle OCI skill collection is included in [`.agents/skills/oci/`](.agents/skills/oci/SKILL.md). Codex can select the `oci` skill for OCI work, or you can invoke it explicitly with `$oci`. Start with its routing table and read the specialist skill that matches the service being implemented. Paths in the upstream skill text such as `oci/functions/...` refer to files under `.agents/skills/oci/` in this repository. The collection is sourced from [Oracle Skills for OCI](https://github.com/oracle/skills/tree/main/oci), and its license is included at [`.agents/skills/oci/LICENSE.txt`](.agents/skills/oci/LICENSE.txt).

Using an OCI skill does not authorize cloud changes. Follow the deployment gate and approval rules in [AGENTS.md](AGENTS.md) before running commands that create or modify OCI resources.

## Source files

- [AGENTS.md](AGENTS.md) defines the workflow, phase gates, OCI guardrails, and implementation rules.
- [usecase.md](usecase.md) contains the customer’s use case and requirements. Use it as product context throughout requirements, design, tasks, and implementation.
- [README.md](README.md) provides the prompts and steps developers use to start and continue the workflow.
