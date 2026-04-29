# grandma3-mcp

`grandMA3` MCP server for safe console inspection and repetitive-task automation.

## Status

Planning and scaffolding.

This repository is starting from documentation first so the implementation can be shaped around proven `grandMA3` workflows and strict safety boundaries.

## Goals

- automate repetitive `grandMA3` operator tasks
- inspect console state safely before generating commands
- use a minimal helper plugin for structured Lua introspection
- keep business logic in the MCP server, not in the MA3 plugin
- distinguish clearly between tested workflows and experimental ones

## Key Design Decisions

- The MCP server will be written in TypeScript.
- v1 transport target is macOS `app_terminal` with `CMDLINE`.
- The server builds and sends MA3 commands itself.
- A minimal helper plugin is used only for structured readback and inspection.
- `DataPool 128` is the MCP sandbox pool.
- Any action outside the sandbox must require explicit user confirmation.
- Experimental workflows must be treated as test-show-only and never silently applied.

## Documentation

- `docs/architecture.md` - server shape, modules, helper plugin role, and tool families
- `docs/safety.md` - confirmation gates, sandbox lifecycle, plugin lifecycle, and risk model
- `docs/workflows.md` - user task mapping, USB staging, and workflow status labels
- `docs/view-xml-research.md` - experimental view export/edit/reimport direction and research plan

## Initial Scope

The first implementation should focus on:

1. raw console connectivity and command execution
2. strict session safety state
3. helper plugin staging/import/run/remove lifecycle
4. read-only inspection tools
5. a few high-value automation workflows

## Out Of Scope For Early v1

- broad show-wide mutation without confirmation
- assuming view XML structure before real exports are captured
- hiding uncertainty behind optimistic automation
