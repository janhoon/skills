---
name: coolify
description: "Operate Coolify-managed applications and servers: trigger deployments, inspect application status/builds/logs, update env vars, and handle rollback-oriented workflows via API/CLI/SSH workflows."
metadata:
  openclaw:
    emoji: "🧊"
---

# Coolify Skill

Use this skill when working with **Coolify-hosted apps** (deploy/redeploy, status checks, env updates, rollout/rollback checks).

## What this skill is for

- Triggering manual deployments/redeploys
- Checking deployment/app health
- Reading logs after deploy failures
- Updating environment variables safely
- Performing guarded rollout/rollback steps

## Required inputs before action

Ask/confirm these once per target app and store in TOOLS.md if stable:

1. Coolify base URL (e.g. `https://coolify.example.com`)
2. API token with app deployment permissions
3. Team/project name
4. Application UUID/name
5. Environment (`production`, `staging`, etc.)

## Safe operation checklist

Before deploy:
- Confirm target app + environment
- Confirm latest branch/commit expected
- Confirm whether DB migrations are included

After deploy:
- Check health endpoint/status
- Check recent logs for startup/runtime errors
- Confirm frontend and API endpoints respond

If failure:
- Capture logs
- Revert env/config if changed
- Redeploy previous known-good commit/image if available

## Suggested workflow template

1. Identify target app + env
2. Trigger deploy
3. Poll deployment status until complete/fail
4. Verify health endpoints
5. Summarize outcome + next actions

## Notes

- Prefer incremental/env-safe changes before full config rewrites.
- Treat production deploys as sensitive actions; call out blast radius.
- Keep rollback path documented for each app.
