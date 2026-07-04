## Hello This is First Commit Testing

### Setup Completed
- GitHub Copilot Chat enabled (Free plan)
- Custom instructions: .github/copilot-instructions.md
- First Agent Skill: .github/skills/hello-skill/SKILL.md
- VS Code setting enabled: chat.useAgentSkills

### Key Finding: On-Demand Loading
Copilot does not always inject the full content of copilot-instructions.md
into every request. It uses "on-demand loading" - the instructions file is
only fully read when the agent decides the current task needs it.

This means simple tasks (like "What is 2+2?") may not trigger the
instructions file to be read, even though the file is correctly configured.
This is expected behavior, not a bug.

### Verified Working
- SKILL.md auto-discovery: confirmed via /hello-skill and natural language trigger
- copilot-instructions.md: confirmed loaded via explicit question
  ("What instruction files are being used?")