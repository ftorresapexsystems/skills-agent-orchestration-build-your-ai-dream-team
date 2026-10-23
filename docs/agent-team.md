# Agent team

For Mona's Project Pulse dashboard, I will use a small custom agent team orchestrated with GitHub Copilot CLI in a Codespace. The definitions live under the repository's `.github/agents/` folder and each agent has a clear specialization and model assignment.

- Planner — Model: Claude Opus 5.5 (copilot). Responsibility: research the repository and requirements, identify edge cases, and create a phased implementation plan the team can execute. Definition: `.github/agents/planner.agent.md`
- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsibility: coordinate work across the team, assign file scopes, sequence phases, and verify the final integration. Definition: `.github/agents/orchestrator.agent.md`
- Designer — Model: Gemini 3.7 Flash (copilot). Responsibility: shape the dashboard UX, visual hierarchy, accessibility, and responsive styling for the Project Pulse experience. Definition: `.github/agents/designer.agent.md`
- Coder — Model: GPT-5.5 (copilot). Responsibility: implement the app logic, fix issues, create support files when needed, and validate behavior within the assigned scope. Definition: `.github/agents/coder.agent.md`

This setup gives us a clear separation of concerns: the Planner defines the approach, the Designer focuses on experience, the Coder builds the implementation, and the Orchestrator coordinates the work through GitHub Copilot CLI in the Codespace environment.
