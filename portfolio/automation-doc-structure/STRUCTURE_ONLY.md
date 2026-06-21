# Structure Only Copy

This file is the public, neutralized structure-only transfer from the private source repository. It intentionally contains no implementation details or source prose.

```text
docs/
|-- ActiveDocs.md
|-- DocsHub.md
|-- Current Phase/
|   |-- PhasePlan.md
|   `-- VerificationChecklist.md
|-- Architecture/
|-- Best Practices/
|-- Wiki/
|   |-- Changelog.md
|   `-- KnowledgeBase.md
|-- Future/
|-- common/
`-- <program>/
    |-- phase_log.md
    `-- action_log.md

.agent/
|-- workflows/
`-- skills/

root guides:
|-- AGENTS.md
`-- agent.md
```

## Required Relationships

```text
AGENTS.md
-> ActiveDocs.md
-> Current Phase + Architecture + Documentation Workflow
-> phase plan + session log + blocker/alternative/review notes
-> common docs, when reusable rules are needed
-> <program> docs, when program-specific evidence is needed
-> changelog/action log, when recent handoff context is needed
```

## Update Routing

```text
structure changed        -> ProjectStructure + compatibility structure entry
runtime changed          -> Runtime/Architecture + user guide
user workflow changed    -> UserManual + walkthrough entry
document policy changed  -> DocumentationWorkflow + AGENTS
new baseline doc created -> ActiveDocs + DocsHub + related index
phase started            -> PhasePlan + owner role + done/QA condition
blocked/reviewed         -> session log + blocker/alternative/review notes
phase QA passed          -> Wiki/KnowledgeBase promotion
meaningful change made   -> Changelog or action log
```
