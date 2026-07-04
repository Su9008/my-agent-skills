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

## Experiment Log: Custom Agent + Skill Conflict

### Setup
- Skill: .github/skills/hello-skill/SKILL.md (requires exact text output)
- Agent: .github/agents/greeting-buddy.agent.md

### Attempt 1: FAILED
Agent instructions:
> You are a cheerful assistant whose only job is to greet people warmly.
> When the user asks for a greeting, use the hello-skill to respond in
> the correct format. Always add one friendly emoji at the end.

tools: []

Result: "CONFIRMED: Hello! Nice to see you." (skill format NOT used)

### Attempt 2: FAILED
Change: tools: [] -> tools: ['read']
Result: Same failure. Skill still not triggered.

### Attempt 3: FAILED
Change: Added explicit Markdown link to skill file:
> [hello-skill](../skills/hello-skill/SKILL.md)

Result: Still failed. Debug logs confirmed hello-skill WAS loaded and
available (11 skills loaded, hello-skill in the list), so the skill
discovery was working correctly. The failure was NOT a loading issue.

### Attempt 4: SUCCESS
Root cause identified: instruction conflict. The persona instruction
("cheerful", "warmly", free-form tone) conflicted with the skill's
strict exact-text requirement. The model prioritized its persona over
the skill's rigid format.

Fix: Rewrote agent instructions to remove ambiguity:
> When the user asks for a greeting, you MUST output ONLY the exact
> text from [hello-skill](../skills/hello-skill/SKILL.md). Do not add
> any other words, warmth, or personality. Output the skill's exact
> text, then add exactly one emoji on a new line after it.

Result: "SKILL-ACTIVATED: Hello from hello-skill! 👋" - SUCCESS

### Key Lesson
When a custom agent's persona instructions are loose/flexible and a
linked skill requires strict/exact output, lightweight models may
prioritize the persona over the skill. Write agent instructions with
explicit, non-negotiable language when exact skill compliance matters.