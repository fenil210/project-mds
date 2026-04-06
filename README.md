# project-mds

A Claude Code plugin that analyzes your codebase and generates project documentation — only the files that actually apply to your project.

## What it generates

| File | Generated when |
|---|---|
| `PROJECT_OVERVIEW.md` | Always |
| `API_COLLECTION.md` | REST, GraphQL, gRPC, or tRPC routes exist |
| `INFRASTRUCTURE.md` | Docker, Kubernetes, Terraform, or cloud IaC exists |
| `DATA_MODELS.md` | ORM schemas, database models, or migrations exist |
| `AUTH_FLOWS.md` | JWT, OAuth, session, or auth middleware exists |
| `ENVIRONMENT_SETUP.md` | `.env.example` exists or 3+ env vars referenced |
| `QUICK_FLOW_WALKTHROUGH.md` | Always |
| `CRON_JOBS.md` | Scheduler library or scheduled tasks exist |
| `AGENTS.md` | Always |

## Install

```bash
/plugin marketplace add fenil210/project-mds
/plugin install project-mds@project-mds
```

## Usage

Navigate to your project root in the terminal, start Claude Code, then run:

```
/project-mds
```

Claude will analyze your codebase, determine which documentation files apply, and generate them in parallel. All files are written to your project root.

## How it works

1. A `project-analyzer` agent scans your codebase and outputs a structured report of what documentation applies.
2. Based on that report, only the relevant writer agents are spawned in parallel.
3. Each writer agent reads actual source files deeply — routes, schemas, configs, worker code — and generates its documentation from real code, not templates.

## Plugin structure

```
project-mds/
├── .claude-plugin/
│   └── plugin.json         # manifest
├── commands/
│   └── project-mds.md      # /project-mds orchestrator
└── agents/
    ├── project-analyzer/   # scans codebase, decides what to generate
    ├── overview-writer/    # PROJECT_OVERVIEW.md
    ├── api-collection-writer/  # API_COLLECTION.md
    ├── infra-writer/       # INFRASTRUCTURE.md
    ├── data-models-writer/ # DATA_MODELS.md
    ├── auth-flows-writer/  # AUTH_FLOWS.md
    ├── env-setup-writer/   # ENVIRONMENT_SETUP.md
    ├── quick-flow-writer/  # QUICK_FLOW_WALKTHROUGH.md
    ├── cron-jobs-writer/   # CRON_JOBS.md
    └── agents-doc-writer/  # AGENTS.md
```

## Requirements

- Claude Code latest version
- Run from your project root directory
