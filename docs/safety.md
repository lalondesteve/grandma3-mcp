# Safety

## Core Principle

This server must behave as if it is attached to a real show unless proven otherwise.

The default stance is conservative:

- read-only by default
- mutation only with explicit intent
- live mutation only after confirmation
- experimental mutation only on disposable test shows

## Workflow Status Labels

Every mutating workflow should advertise one of these states:

- `tested`
- `partially tested`
- `experimental`

These labels are part of the tool response, not just documentation.

## Required Guarantees

The server must:

1. default mutating tools to `dryRun: true`
2. show exact target objects before applying live changes
3. show exact planned commands before applying live changes
4. state whether sandbox validation was used
5. state whether the workflow is tested or experimental
6. restore the prior selected `DataPool` whenever possible
7. clean up temporary plugin state it created
8. stop and ask when the environment is ambiguous

## Strict Confirmation Policy

Live changes outside the sandbox require explicit user confirmation.

Examples:

- renaming existing views in a show
- rewriting real macro lines
- importing edited XML into an active object slot
- deleting or overwriting existing show data

Experimental workflows must require a stronger acknowledgment:

- the user confirms the target is a disposable test show
- the user confirms the workflow is not yet production-safe

## Sandbox DataPool Policy

`DataPool 128` is the reserved MCP sandbox.

Session rules:

1. record the original selected `DataPool`
2. check whether `DataPool 128` already exists
3. if it already exists, ask the user before using it
4. if it does not exist, create it
5. select it for sandbox work
6. keep it available for the session
7. clean sandbox-created objects after each operation when possible
8. if the server created `DataPool 128`, it may delete it automatically at session end
9. if `DataPool 128` existed before the session, never delete it automatically

## Cleanup Rules

If the server created a temporary resource, it owns cleanup.

This includes:

- `DataPool 128` when created by the server
- helper plugin pool object when created by the server
- temporary exported or staged helper-plugin files when the staging path is known and safe

If ownership is uncertain, ask the user before deleting.

## Helper Plugin Safety

The helper plugin should be imported into the last available plugin slot.

Rules:

1. discover the last available slot at runtime
2. stage files through the shared USB path
3. import the plugin into that slot
4. reload plugins
5. run read-only helper actions
6. remove the plugin object afterward if the server created it

The helper plugin should avoid:

- UI prompts
- blocking confirmations
- broad mutation
- hidden stateful behavior

## USB Ambiguity Policy

USB staging should only proceed when the server can reasonably correlate:

- a console-visible drive number
- a local machine mount path
- the expected MA3 library folder structure

If any part is ambiguous, the server must ask before using it.

## Risk Classes

### High Risk

- show-wide rename or rewrite operations
- XML export/edit/reimport against live show data
- deleting objects whose ownership is unclear

### Medium Risk

- temporary plugin import lifecycle
- sandbox creation and selection
- output parsing errors that could misread console state

### Low Risk

- pure inspection workflows
- local documentation and planning logic
- local output parsing tests using captured fixtures

## Presently Known Confidence Levels

### Tested

- macOS `app_terminal` remote flow
- `CMDLINE` command execution
- USB-staged plugin import using `If Drive X`
- `ReloadAllPlugins`
- macro line command rewriting patterns
- Lua handle-based inspection primitives

### Partially Tested

- session sandbox lifecycle around `DataPool 128`
- helper plugin lifecycle automation
- structured readback through a temporary helper plugin

### Experimental

- view XML export/edit/reimport automation
- generalized show-wide mutation workflows
- any workflow without a real captured console example in this repo
