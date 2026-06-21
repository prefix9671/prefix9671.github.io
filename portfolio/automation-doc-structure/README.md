# Neutralized Documentation Structure Case

Source context: a private UI automation repository was reviewed for document architecture only. This public draft does not copy private implementation content, proprietary product details, screenshots, environment values, credentials, logs, or customer-specific evidence.

## Purpose

This draft captures a portfolio plan for explaining a documentation system designed for AI-assisted automation work. The emphasis is not the original product domain, but the operating model:

- how a large automation repository exposes the right starting point to humans and AI agents
- why active documents, current phase notes, architecture docs, and update policy are separated
- how agent handoff context is restored across sessions
- how documentation updates are routed after code, tool, or process changes

## Neutralized Structure Map

```text
repository-root/
|-- AGENTS.md
|-- agent.md
|-- .agent/
|   |-- workflows/
|   |   `-- restore_context.md
|   `-- skills/
|       `-- doc_manager/
|           `-- SKILL.md
|-- docs/
|   |-- index.md
|   |-- ActiveDocs.md
|   |-- DocsHub.md
|   |-- Current Phase/
|   |   |-- CurrentPhase.md
|   |   `-- VerificationChecklist.md
|   |-- Architecture/
|   |   |-- SystemOverview.md
|   |   `-- RuntimeAndPackaging.md
|   |-- Best Practices/
|   |   |-- DocumentationWorkflow.md
|   |   `-- EngineeringGuidelines.md
|   |-- Wiki/
|   |   |-- ProjectStructure.md
|   |   |-- UserManual.md
|   |   `-- Changelog.md
|   |-- Future/
|   |   |-- Roadmap.md
|   |   `-- IdeasAndBacklog.md
|   |-- common/
|   |   |-- index.md
|   |   |-- program_spec_tc_automation_framework.md
|   |   |-- templates.md
|   |   `-- asset_submission_guide.md
|   |-- <program>/
|   |   |-- index.md
|   |   |-- 10_screen_specs/
|   |   |-- 20_query_modules/
|   |   |-- 30_test_cases/
|   |   |-- 40_report_validation/
|   |   |-- 50_developer_debugging/
|   |   |-- 60_runtime_or_agent_integration/
|   |   |-- 90_templates.md
|   |   |-- 99_todo.md
|   |   `-- assets/
|   |       |-- README.md
|   |       `-- asset_submission_guide.md
|   |-- project_structure.md
|   |-- walk_updates.md
|   `-- walkthrough.md
|-- config/
|-- resources/
|-- src/
|-- tools/
`-- dist/
```

## Why This Structure

The structure is intentionally split into layers.

| Layer | Reason |
|---|---|
| Root agent guides | Give every session a short, consistent entry point before reading deeper docs. |
| `ActiveDocs.md` | Acts as the single active-document registry so old or experimental docs do not silently become policy. |
| `DocsHub.md` | Gives humans and agents a readable map of where to start by task type. |
| `Current Phase/` | Keeps the present goal, risks, and acceptance criteria separate from historical wiki pages. |
| `Architecture/` | Describes current implementation and runtime boundaries. |
| `Best Practices/` | Holds policies that should survive individual feature changes. |
| `Wiki/` | Stores reference material, user-facing guidance, and changelog history. |
| `Future/` | Keeps ideas visible without letting them override current behavior. |
| `common/` | Extracts reusable planning, test-case, template, and asset rules. |
| `<program>/` | Contains the most specific screen, query, test, report, runtime, and asset documents. |
| Compatibility entries | Keeps older links alive while routing readers to the newer active documents. |

## Agent Context Sharing Intent

The repository structure was designed so that different AI agents can recover context without relying on chat history.

1. `AGENTS.md` defines the repository-level working rules and required reading path.
2. `agent.md` provides a shorter compatibility entry for fast session startup.
3. `.agent/workflows/restore_context.md` describes how a new session reconstructs the current project state.
4. `.agent/skills/doc_manager/SKILL.md` routes document updates by change type.
5. `ActiveDocs.md` identifies which documents are authoritative.
6. `Current Phase`, `Architecture`, and `DocumentationWorkflow` form the minimum context set.
7. `Changelog` and action-log style documents preserve recent decisions and handoff history.

This creates a repeatable handoff loop:

```text
Start session
-> Read active document registry
-> Read current goal, architecture, and documentation workflow
-> Add program-specific docs only when task scope requires it
-> Implement or edit
-> Update affected active docs and compatibility entries
-> Record meaningful changes for the next agent
```

## Documentation Policy And Update Guide

The observed policy can be summarized as follows.

- Plans or non-trivial changes start from the active document registry.
- Documents not registered as active are treated as inactive, historical, or compatibility-only.
- Current operational truth outranks architecture, architecture outranks policy, policy outranks wiki, and wiki outranks future plans.
- When a change affects structure, runtime, user workflow, program docs, or policy, the matching active document must be updated in the same change.
- New baseline documents must be registered in both the active registry and the document hub.
- Compatibility files should stay short and point to the current authoritative document.
- Program-specific details should not leak into common templates.
- Common templates and asset rules should be reusable across future programs.

## Portfolio Addition Plan

Working title: **Documentation Operating System for AI-assisted Automation**

Recommended portfolio angle:

- **Problem:** AI agents and humans lose context when automation work spans code, UI evidence, test cases, runtime constraints, and historical decisions.
- **Approach:** Build a docs-first repository map with an active-document registry, layered authority, and explicit context-restoration workflow.
- **Action:** Separate current phase, architecture, best practices, wiki, future backlog, common templates, and program-specific detail docs.
- **Agent Handoff:** Use root guides, restore workflow, document-manager skill, changelog, and action logs to let each agent reconstruct task context.
- **Governance:** Require active-document registration, update triggers, conflict-resolution rules, and compatibility entry maintenance.
- **Result To Claim Carefully:** A repeatable documentation structure that reduces context loss and makes AI-assisted maintenance safer and easier to resume.

## Public Portfolio Boundary

When this draft is promoted into `index.html`, keep it neutral.

- Use generic terms such as "desktop automation target", "program-specific docs", "runtime integration", and "report validation".
- Do not include private repository names except where the user explicitly wants the public GitHub link shown.
- Do not include actual customer names, environment paths, screenshots, credentials, database names, internal tool names, logs, or proprietary UI labels.
- Show the structure and operating policy, not source content.
