# Mona's Project Pulse agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate a four-agent team for Mona's Project Pulse dashboard. The **Orchestrator** begins by obtaining a plan, turns it into dependency-aware phases with explicit file ownership, coordinates the specialists, and verifies the integrated result. The **Planner** researches the repository and relevant documentation, identifies risks and edge cases, and produces the implementation plan; the **Coder** implements the assigned application behavior and runnable-app support; and the **Designer** shapes the dashboard's UX, accessibility, responsive layout, and visual polish.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Coordinates planning, coding, and design; sequences dependent work and integrates the outcome without implementing it directly. | [`.github/agents/orchestrator.agent.md`](../.github/agents/orchestrator.agent.md) |
| Planner | Claude Opus 4.7 (copilot) | Researches the codebase and dependencies, then creates an implementation plan with assignments, dependencies, validation expectations, risks, and open questions. | [`.github/agents/planner.agent.md`](../.github/agents/planner.agent.md) |
| Coder | GPT-5.5 (copilot) | Implements scoped code changes with explicit errors and testable behavior; when assigned the runnable Project Pulse app, can also add its deterministic VS Code launch configuration. | [`.github/agents/coder.agent.md`](../.github/agents/coder.agent.md) |
| Designer | Gemini 3.1 Pro (copilot) | Defines and implements scoped UI/UX work, including accessible information hierarchy, responsive behavior, project cards, status badges, priority treatment, and visual clarity. | [`.github/agents/designer.agent.md`](../.github/agents/designer.agent.md) |

All specialists stay within the files assigned by the Orchestrator, and none of the agents stages, commits, or pushes changes; Git operations remain under the learner's control through Copilot CLI prompts.
