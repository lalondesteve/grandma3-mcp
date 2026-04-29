# Workflows

## Purpose

This document maps target user requests into concrete server workflows.

## General Pattern

Most useful MA3 automation requests follow the same sequence:

1. inspect objects
2. derive mapping or intended state
3. preview exact commands
4. sandbox-test when possible
5. require live confirmation if the real target cannot stay inside the sandbox
6. apply
7. verify

## Example: Create Macros For A Preset Range

User intent example:

```text
Check presets at preset pool 17.100 thru 17.200 and create macros calling those presets.
```

Suggested workflow:

1. inspect preset range and labels
2. determine target macro numbering and naming rule
3. build macro-line command text
4. preview object creation plan
5. create equivalent test macros in `DataPool 128`
6. verify labels and line command text through helper-plugin inspection
7. if approved, apply to live target range
8. verify live result

Expected tool involvement:

- `ma3_inspect_presets`
- `ma3_preview_plan`
- `ma3_create_macros_for_presets`

Status target:

- `partially tested` after sandbox validation exists

## Example: Rename Views From Sequence Names

User intent example:

```text
Rename all views from 100 to 200 to reflect the name of the sequences 201, 221, 241 and so forth.
```

Suggested workflow:

1. inspect target views
2. inspect sequence names using start `201` and step `20`
3. build view-to-sequence mapping
4. preview exact rename operations
5. require explicit confirmation because this is a live rename operation
6. apply rename commands
7. verify readback

Notes:

- the conceptual mapping is straightforward
- the live rename should still be treated conservatively until console-tested in this repo

Status target:

- initially `experimental`
- move to `partially tested` after a confirmed console run

## Example: Confirm Macro `$song` Values

User intent example:

```text
Confirm that macros 50 thru 60 have the proper variable value stored for uservar $song in cmdline 1.
```

Suggested workflow:

1. inspect macro line 1 command text through the helper plugin
2. parse `SetUserVariable "song" "..."`
3. report pass, fail, missing, or malformed per macro

Expected tool involvement:

- `ma3_inspect_macro_lines`
- `ma3_check_macro_song_variable`

Status target:

- one of the earliest workflows to move toward `tested`

## Shared USB Staging Workflow

Some tasks require file staging between the local machine and the console.

Examples:

- helper plugin import
- XML export/reimport workflows
- future library-based object exchange

The server should have a dedicated shared-drive detection step.

Workflow:

1. inspect console-visible drives
2. inspect local macOS mount paths
3. check expected MA3 folder structure on the candidate drive
4. if one clear mapping exists, propose it
5. if multiple or uncertain mappings exist, ask the user

Expected tool involvement:

- `ma3_detect_shared_usb`

## Helper Plugin Session Workflow

The helper plugin should be fully server-managed.

Workflow:

1. detect shared USB staging path
2. discover last available plugin slot
3. stage helper plugin files to USB
4. import helper plugin into temporary slot
5. reload plugins
6. run helper actions as needed
7. remove helper plugin object when finished

This workflow is essential because the server depends on clean structured introspection, but the plugin itself should remain disposable.

## Experimental View XML Workflow

User intent example:

```text
Make preset pool 17 show pools starting at 200 on view "foo".
```

Planned workflow:

1. locate target view object
2. export the view to shared USB storage
3. copy XML to local staging
4. inspect the XML structure
5. preview exact XML edits
6. require explicit disposable-show confirmation
7. write the edited XML
8. re-stage to USB
9. import the XML back into the console
10. verify behavior

Current status:

- `experimental`

Reason:

- no captured view export samples exist in this repo yet
- XML structure and safe edit boundaries are not yet proven here
