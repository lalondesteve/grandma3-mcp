# Architecture

## Purpose

`grandma3-mcp` is a hybrid MCP server for `grandMA3`.

It combines:

- direct MA3 command execution from the server
- a minimal MA3 helper plugin for structured Lua introspection
- a strict safety layer that defaults to read-only behavior

The plugin should stay simple. The MCP server should own orchestration, planning, command generation, risk checks, and user confirmation gates.

## High-Level Shape

The server has three main subsystems.

1. Transport
2. Introspection
3. Safety and workflow orchestration

## Transport

The initial transport target is the tested macOS terminal workflow:

- MA3 terminal binary inside the app bundle
- `CMDLINE` for command execution
- optional `SYSNOW` later for monitoring

The transport layer should:

- open terminal sessions
- send command batches in a controlled order
- capture raw output
- normalize success and failure results
- expose timeouts and low-level diagnostics

## Introspection

The server should not rely entirely on parsing human-oriented `List` output.

Instead, a helper plugin should be used for structured readback of:

- object labels
- object existence
- macro line command text
- sequence naming ranges
- view naming ranges
- preset naming ranges
- sandbox and plugin-slot state

The plugin should emit stable machine-readable line records using `Printf()`.

Example shape:

```text
MCP|OK|action=inspect_macro_lines
MCP|ITEM|type=macro|number=50|line=1|command=SetUserVariable "song" "hello_v"
MCP|ERR|message=Macro 52 line 1 missing
```

JSON inside MA3 Lua is optional. Line-oriented records are sufficient for v1 and simpler to keep stable.

## Safety And Workflow Orchestration

The orchestration layer should be the highest authority in the system.

It decides:

- whether a tool is read-only or mutating
- whether a workflow is tested, partially tested, or experimental
- whether sandbox execution is possible
- whether explicit user confirmation is required
- whether a helper plugin may be staged and imported
- whether cleanup is safe to perform automatically

## Proposed Internal Modules

Suggested TypeScript module layout:

- `src/transport/terminal.ts`
- `src/transport/session.ts`
- `src/transport/parser.ts`
- `src/ma3/commands.ts`
- `src/ma3/quoting.ts`
- `src/ma3/ranges.ts`
- `src/plugin/lifecycle.ts`
- `src/plugin/actions.ts`
- `src/safety/session-state.ts`
- `src/safety/confirmation.ts`
- `src/safety/sandbox.ts`
- `src/usb/detection.ts`
- `src/tools/*.ts`

## Tool Families

### Transport Tools

- `ma3_ping_console`
- `ma3_cmdline`
- `ma3_cmdline_batch`

### Safety And Environment Tools

- `ma3_get_selected_datapool`
- `ma3_prepare_session_sandbox`
- `ma3_cleanup_session_sandbox`
- `ma3_detect_shared_usb`
- `ma3_check_helper_plugin_slot`

### Helper Plugin Lifecycle Tools

- `ma3_stage_helper_plugin`
- `ma3_import_helper_plugin`
- `ma3_run_helper_plugin_action`
- `ma3_remove_helper_plugin`

### Read-Only Inspection Tools

- `ma3_inspect_presets`
- `ma3_inspect_sequences`
- `ma3_inspect_views`
- `ma3_inspect_macro_lines`
- `ma3_check_macro_song_variable`

### Automation Tools

- `ma3_create_macros_for_presets`
- `ma3_rename_views_from_sequences`
- `ma3_repair_macro_song_variables`

### Experimental View Tools

- `ma3_export_view_xml`
- `ma3_preview_view_xml_edit`
- `ma3_import_view_xml`

## Default Execution Model

The default flow for mutating tools should be:

1. inspect current state
2. compute target mapping
3. present exact plan
4. decide whether sandbox validation is possible
5. run sandbox validation when available
6. request confirmation if live changes are required
7. apply commands
8. verify results
9. run cleanup

## Design Boundaries

The helper plugin should not become a second automation engine.

Avoid moving these concerns into Lua unless there is a strong reason:

- command planning
- bulk rename logic
- business-specific mapping rules
- safety policy
- confirmation logic

Those belong in the MCP server.
