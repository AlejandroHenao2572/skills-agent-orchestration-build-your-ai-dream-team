# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate a four-agent team for Mona's Project Pulse dashboard. The Orchestrator will coordinate the specialists, keep file ownership clear, sequence dependent work, and verify that the integrated result works.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Breaks the dashboard request into phases, delegates work to the specialists, manages dependencies and file scope, and reports the integrated outcome. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, edge cases, risks, and validation needs, then produces an implementation plan. The Planner does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Defines and implements the dashboard's UI/UX, information hierarchy, accessibility, responsive behavior, and visual styling, including project cards, status badges, priority treatment, and deterministic CSS hooks. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the assigned application logic and supporting runnable-app configuration with clear, deterministic, testable behavior, explicit errors, and validation. | `.github/agents/coder.agent.md` |

The Orchestrator should start with the Planner, coordinate the Designer and Coder according to their file assignments and dependencies, and then verify the completed Project Pulse dashboard as a cohesive, usable result.
