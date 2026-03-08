# skills

A collection of OpenCode/OpenClaw skills for common development workflows.

## Available Skills

| Skill | Description |
|-------|-------------|
| `fizzy` | Manage Fizzy kanban boards and cards via CLI. Includes the Ralph loop protocol — agent ownership tagging, Ralphing column transitions, and done state management. |
| `outline-wiki-content` | Draft, restructure, and maintain documentation for Outline-based wikis. |
| `coolify` | Operate Coolify-managed apps — trigger deploys, check status, update env vars, handle rollbacks. |

## Repository Structure

```
skills/
├── fizzy/
│   └── SKILL.md
├── outline-wiki-content/
│   ├── SKILL.md
│   └── references/
│       └── api_reference.md
└── coolify/
    └── SKILL.md
```

## Installation

### OpenClaw

Copy any skill into your OpenClaw workspace skills directory:

```bash
gh repo clone janhoon/skills
cd skills

mkdir -p ~/.openclaw/workspace/skills
cp -R skills/fizzy ~/.openclaw/workspace/skills/
cp -R skills/outline-wiki-content ~/.openclaw/workspace/skills/
cp -R skills/coolify ~/.openclaw/workspace/skills/
```

To install all skills at once:

```bash
cp -R skills/* ~/.openclaw/workspace/skills/
```

### OpenCode

Copy any skill into your OpenCode skills directory:

```bash
gh repo clone janhoon/skills
cd skills

mkdir -p ~/.config/opencode/skills
cp -R skills/fizzy ~/.config/opencode/skills/
cp -R skills/outline-wiki-content ~/.config/opencode/skills/
cp -R skills/coolify ~/.config/opencode/skills/
```

To install all skills at once:

```bash
cp -R skills/* ~/.config/opencode/skills/
```

## Verify Installation

### OpenClaw
```bash
ls ~/.openclaw/workspace/skills/
```

### OpenCode
```bash
ls ~/.config/opencode/skills/
```

## Updating

To pull the latest version of all skills:

```bash
cd ~/path/to/skills
git pull
cp -R skills/* ~/.openclaw/workspace/skills/    # OpenClaw
cp -R skills/* ~/.config/opencode/skills/        # OpenCode
```
