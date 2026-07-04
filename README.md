# SWEOrchestrAI Local Service

Go local execution service for SWEOrchestrAI.

**Cloud-managed software delivery. Locally executed AI engineering.**

## Purpose

The `local-service` repository implements the local execution service for SWEOrchestrAI.

It is the core of the Local Execution Plane. It connects the desktop app with local repositories, providers, MCP tools, command execution, approval gates, audit logs, and cloud sync.

## Architecture Role

```text
local-app
  -> REST + WebSocket over localhost
local-service
  -> Local providers, repositories, MCP tools, commands, audit logs
  -> authenticated sync APIs
api
```

The `local-service` performs local execution work.

## Planned Stack

- Go
- Localhost REST API
- WebSocket event streaming
- Provider adapter interfaces
- Local audit log
- Workspace-scoped permissions
- Config file
- Optional SQLite for local metadata/audit persistence
- Docker only where useful for development/testing

## Responsibilities

The `local-service` repository is responsible for:

- exposing a localhost API to `local-app`,
- exposing WebSocket run event streams,
- registering local workspaces,
- managing provider adapters,
- detecting provider capabilities,
- running mock provider workflows,
- integrating future providers such as Claude Code, Codex CLI, Ollama, and OpenCode,
- integrating MCP tools,
- interacting with local repositories,
- classifying risky actions,
- requesting approvals before sensitive operations,
- executing approved local commands,
- maintaining local audit logs,
- syncing execution metadata with `api`.

## Non-Responsibilities

The `local-service` repository is not responsible for:

- public cloud APIs,
- cloud project persistence,
- cloud authentication UI,
- GitHub organization profile content,
- cloud project management UI,
- executing destructive actions without approval,
- exposing arbitrary command execution endpoints,
- requiring source code upload to cloud.

## Security Rules

Minimum MVP security expectations:

- bind only to `127.0.0.1`,
- require a local session token,
- validate request origin where applicable,
- avoid arbitrary command execution endpoints,
- restrict operations to registered workspaces,
- separate read-only, write, and destructive actions,
- approval-gate risky operations,
- avoid leaking secrets in logs or sync payloads,
- maintain local audit logs.

## Initial API Surface

Planned REST endpoints:

```http
GET /health
GET /capabilities
GET /providers
POST /providers/{providerId}/test
GET /workspaces
POST /workspaces
POST /runs
GET /runs/{runId}
POST /runs/{runId}/cancel
GET /approvals/pending
POST /approvals/{approvalId}/approve
POST /approvals/{approvalId}/reject
```

Planned WebSocket streams:

```text
WS /runs/{runId}/stream
WS /events
```

## Initial Internal Modules

Planned structure:

```text
cmd/
  local-service/
    main.go
internal/
  api/
  runtime/
  providers/
  mock/
  workspaces/
  approvals/
  audit/
  security/
  sync/
  config/
```

## MVP Scope

Initial local-service foundation should support:

- health endpoint,
- capabilities endpoint,
- provider registry,
- mock provider,
- workspace registration,
- execution run creation,
- WebSocket run event stream,
- approval request lifecycle,
- local audit logging.

## First Demo Goal

The first local-service demo should prove this flow:

```text
1. local-app connects to local-service.
2. user starts a mock run.
3. local-service streams run logs.
4. mock provider requests approval.
5. user approves or rejects in local-app.
6. local-service completes or cancels the run.
7. result metadata is prepared for sync with api.
```

## Related Repositories

| Repository | Relationship |
|---|---|
| [`overview`](https://github.com/SWEOrchestrAI/overview) | Architecture, roadmap, ADRs, requirements, and contracts. |
| [`local-app`](https://github.com/SWEOrchestrAI/local-app) | Desktop app that consumes `local-service`. |
| [`api`](https://github.com/SWEOrchestrAI/api) | Cloud backend that receives synced execution metadata. |
| [`web`](https://github.com/SWEOrchestrAI/web) | Cloud project management UI that displays synced history. |

## Current Status

MVP foundation planning. Implementation has not been scaffolded yet.

## License

Copyright (c) 2026.

This repository is currently shared for portfolio and documentation purposes only. No license is granted for reuse, redistribution, or derivative works unless explicitly stated.
