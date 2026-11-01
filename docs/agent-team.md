# Project Pulse agent team

Project Pulse is coordinated in GitHub Copilot CLI from a GitHub Codespaces workspace. The Orchestrator assigns clear file scopes, keeps dependent work sequential, and uses specialist output to guide the next phase.

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the team, turns the plan into dependency-aware phases, assigns file ownership, verifies the integrated result, and records handoffs. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and produces implementation steps, ownership, dependencies, risks, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements assigned application and support files with explicit, testable behavior and validates the changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Defines the Project Pulse UI/UX direction, accessibility, information hierarchy, responsive behavior, and visual design. | `.github/agents/designer.agent.md` |

## Collaboration flow

1. In Copilot CLI, the Orchestrator asks the Planner for the Project Pulse implementation plan.
2. The Orchestrator converts that plan into phases and assigns non-overlapping file scopes.
3. The Designer provides dashboard direction and accessibility requirements before or alongside independent planning work.
4. The Coder implements the approved static dashboard and runnable support files within its assigned scope.
5. The Orchestrator verifies the combined work, records each handoff in `docs/handoff.md`, and reports the outcome. Dependent or overlapping work stays sequential; independent work may run in parallel.
