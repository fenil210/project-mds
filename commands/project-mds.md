# /project-mds

You are orchestrating a documentation generation workflow for the current project. Your job is to analyze the codebase and generate only the markdown files that are genuinely applicable — not a generic template dump.

## Step 1: Run the Project Analyzer

Use the Task tool to invoke the `project-analyzer` agent. Pass it the following instruction:

> Analyze this project and return a structured JSON report of what documentation files should be generated. The working directory is the current project root.

Wait for the analyzer to complete and parse its output. The output will be a JSON object like:

```json
{
  "project_name": "...",
  "primary_language": "...",
  "frameworks": [],
  "applicable_docs": {
    "PROJECT_OVERVIEW": true,
    "AGENTS": false,
    "API_COLLECTION": true,
    "INFRASTRUCTURE": true,
    "QUEUE_FLOW_WALKTHROUGH": false,
    "DATA_MODELS": true,
    "AUTH_FLOWS": true,
    "ENVIRONMENT_SETUP": true,
    "CRON_JOBS": false
  },
  "context": {
    "has_docker": true,
    "has_kubernetes": false,
    "queue_system": null,
    "api_framework": "express",
    "auth_strategy": "jwt",
    "db_orm": "prisma",
    "ai_agents": false,
    "has_cron": false,
    "cloud_provider": "aws"
  }
}
```

## Step 2: Spawn Writer Agents in Parallel

Based on the `applicable_docs` map from the analyzer output, invoke ONLY the applicable writer agents using the Task tool. Invoke all applicable agents in parallel — do not wait for one before starting the next.

Map of doc keys to agents:
- `PROJECT_OVERVIEW` → `overview-writer`
- `AGENTS` → `agents-doc-writer`
- `API_COLLECTION` → `api-collection-writer`
- `INFRASTRUCTURE` → `infra-writer`
- `QUEUE_FLOW_WALKTHROUGH` → `queue-flow-writer`
- `DATA_MODELS` → `data-models-writer`
- `AUTH_FLOWS` → `auth-flows-writer`
- `ENVIRONMENT_SETUP` → `env-setup-writer`
- `CRON_JOBS` → `cron-jobs-writer`

When invoking each writer agent, pass the full analyzer `context` object as part of the instruction so the agent has project-wide awareness.

## Step 3: Report Results

After all agents complete, print a summary of every file that was generated, with its full path. If any agent failed or produced an empty file, flag it clearly so the user knows.

Format:
```
✓ PROJECT_OVERVIEW.md
✓ API_COLLECTION.md
✓ INFRASTRUCTURE.md
✓ DATA_MODELS.md
✓ AUTH_FLOWS.md
✓ ENVIRONMENT_SETUP.md

Skipped (not applicable to this project):
  - AGENTS.md
  - QUEUE_FLOW_WALKTHROUGH.md
  - CRON_JOBS.md
```

## Rules

- Never generate a file if the analyzer marked it as `false`. Do not guess or assume.
- Always write files to the project root directory (the directory where Claude Code was launched).
- If the project root already contains one of these files, ask the user whether to overwrite before proceeding.
- Do not add any preamble or explanation text into the generated markdown files themselves — just the content.
