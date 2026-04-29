# View XML Research

## Status

Experimental.

This workflow should be treated as test-show-only until the repository contains real captured view exports and successful reimport evidence.

## Motivation

Editing views directly on the console is often slow and repetitive.

If view XML exports can be safely understood and patched, the server could eventually support tasks such as:

- changing pool window numbering
- adjusting pool window targets
- renaming view objects
- making repetitive window-layout changes faster than manual UI work

## What Is Known

The official manual confirms:

- views are exportable and importable object types
- the `View` keyword exists and supports normal object operations
- command-line `Export` and `Import` support XML-based object exchange

## What Is Not Yet Known Here

- real exported view XML structure from a console or onPC session
- whether all important pool-window settings round-trip cleanly through XML
- which nodes or attributes define pool type, target object number, and layout details
- whether importing an edited view has hidden dependencies or side effects

## Required Research Sequence

Before writing implementation code for automated view XML edits, collect this evidence in a disposable show:

1. export one simple view object to XML
2. export one more complex view with at least one preset pool window
3. compare the XML files
4. identify root object structure and child window structure
5. identify fields that appear to control:
   - pool type
   - pool number
   - datapool reference
   - geometry
   - title or name metadata
6. import the unchanged XML into a sacrificial target slot
7. verify the import round trip
8. edit one harmless field
9. reimport and verify what changed
10. document the result with evidence labels

## Desired Output From Research

The repository should eventually contain:

- one or more captured example view XML files
- redacted or minimal diffs showing meaningful field changes
- documentation describing confirmed fields and uncertain fields
- at least one tested import/edit/reimport example

## Rules For Implementation

Until the above evidence exists:

- do not label view XML editing as production-safe
- do not apply view XML edits to a real show without explicit disposable-show confirmation
- do not assume naming conventions inside the XML without captured proof
