# OpenClaw Skills

A community-driven collection of skills for [OpenClaw](https://openclaw.ai) agents.

## What are Skills?

Skills are modular abilities that teach OpenClaw agents new capabilities. Each skill contains:
- `SKILL.md` - Tool definition and usage instructions
- `_meta.json` - Skill metadata
- `scripts/` - Executable scripts (Python, JS, etc.)

## Available Skills

| Skill | Description | Author |
|-------|-------------|--------|
| [capminal-skill](./capminal-skill) | Interact with Cap Wallet via Capminal API | AndreaPN |

## Installation

### Via OpenClaw Chat
```
install this skill: https://github.com/user/openclaw-skills/tree/main/skill-name
```

### Manual Installation
```bash
git clone https://github.com/user/openclaw-skills.git
cp -r openclaw-skills/skill-name ~/.openclaw/skills/
```

## Contributing

We welcome contributions! To add a new skill:

1. **Fork** this repository
2. **Create** a new folder for your skill: `your-skill-name/`
3. **Add** required files:
   ```
   your-skill-name/
   ├── SKILL.md        # Required: Tool definition
   ├── _meta.json      # Required: Metadata
   └── scripts/        # Optional: Executable scripts
   ```
4. **Submit** a Pull Request

### SKILL.md Template

```markdown
---
name: Your Skill Name
description: What your skill does
version: 1.0.0
author: YourName
tags: [tag1, tag2]
requires: [ENV_VAR_IF_NEEDED]
---

## How to use this skill

You have access to a tool called `your_tool_name`.

**Tool signature / schema**
\```tool
{
  "name": "your_tool_name",
  "description": "Tool description",
  "parameters": {
    "type": "object",
    "properties": {
      "param1": {
        "type": "string",
        "description": "Parameter description"
      }
    },
    "required": ["param1"]
  }
}
\```

## Execution instructions

Instructions for the agent on how to use this tool.
```

### _meta.json Template

```json
{
  "owner": "your-username",
  "package": "your-skill-name",
  "displayName": "Your Skill Name",
  "latestRelease": {
    "version": "1.0.0",
    "publishedAt": 1234567890000
  }
}
```

## Guidelines

- Keep skills focused on a single purpose
- Include clear documentation in SKILL.md
- Handle errors gracefully in scripts
- Use environment variables for sensitive data (API keys)
- Test your skill before submitting

## License

MIT License - See [LICENSE](./LICENSE) for details.

## Links

- [Capminal](https://www.capminal.ai/)
- [OpenClaw Documentation](https://openclaw.ai/docs)
- [ClawHub Marketplace](https://www.clawhub.ai/skills)
- [Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills)
