[[*TOC*]]

# Overview / Summary

`Hide Choice Options.js` is a form script for model-driven apps that hides (removes) specific options from a Choice (Option Set) field on a form, for both the main form control and the header control, based on configuration stored in a Sensei Config Setting record rather than hard-coded logic.

The script reads a JSON configuration (`HideChoiceConfig`) from the `se_senseiconfigsettings` table, matches the current entity/table, and applies one or more rules that remove the configured options from the target Choice field's control(s). Rules can match by option **value** (numeric) or option **label** (text), and can be scoped to the field's body control, header control, or both. Currently selected values are never removed, so a record that already has a "hidden" option selected will still display it correctly.

For background on the design and intended usage, see:

- [Hide Choice Fields - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/13437/Hide-Choice-Fields)
- [Change Requests - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/12210/Change-Requests)

# Backlog Item/s

- #TBC

# Related Item/s

The above backlog items are related to the following item/s

- N/A

# Links

- [Hide Choice Fields - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/13437/Hide-Choice-Fields)
- [Change Requests - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/12210/Change-Requests)

# Navigating to the Changes

TBC

# Changes Implemented

## Form Changes

The `Altus.HideChoice` script is registered as an `OnLoad` event handler on the form(s) that need choice options hidden. On load, it:

1. Reads the `HideChoiceConfig` setting from `se_senseiconfigsettings` (via `se_value`, filtered by `se_logicalname`).
2. Parses the JSON config and finds the entry under `entitiesTable` matching the current table (entity) logical name.
3. Iterates the `choiceColumns` rules for that entity and applies each one to the form.
4. Re-wires itself to reapply the rules whenever the `statuscode` field changes (e.g. after a status transition), so options stay hidden/visible correctly as the record changes state.

### Configuration Structure (`HideChoiceConfig`)

The setting value is JSON shaped roughly like:

```json
{
  "entitiesTable": [
    {
      "entity": { "value": "<table logical name>" },
      "choiceColumns": [
        {
          "targetAttributeColumn": { "value": "<choice field logical name>" },
          "targetControl": "<choice field logical name (fallback)>",
          "hideOption": true,
          "matchMode": "value | label",
          "applyTo": "body | header | both",
          "targetValues": [ "<option value or label>", ... ]
        }
      ]
    }
  ]
}
```

### Rule Behaviour

| Property | Description |
| --- | --- |
| `hideOption` | If explicitly `false`, the rule is skipped entirely. |
| `targetAttributeColumn` / `targetControl` | The logical name of the Choice field to target. Defaults to `statuscode` if not supplied. |
| `matchMode` | `value` (default) removes options by their numeric option set value. `label` removes options whose displayed text matches (case-insensitive). |
| `applyTo` | `body` only affects the field on the main form. `header` only affects the header control. Anything else (including omitted) applies to both. |
| `targetValues` | List of option values or labels to remove. Accepts plain strings/numbers or objects with a `choiceOptions` property. |

### Safety Behaviour

- If the field's currently selected value matches an option targeted for removal, that option is **not** removed, so the field still displays the record's existing value correctly.
- Errors reading config, parsing JSON, or manipulating an individual control are caught and logged to the console rather than breaking form load.
- If no configuration is found, or the current entity has no matching config, the script exits without making changes.

<!-- PLACE SCREENSHOT/S HERE -->

## Views

- N/A - this change does not affect views.

## Reporting

- N/A - this change does not affect reporting.

## List of New / Modified Columns

| Field Name | Type | Description |
| --- | --- | --- |
| `se_senseiconfigsettings` (`se_logicalname` = `HideChoiceConfig`) | Config record | Stores the JSON configuration consumed by this script to determine which choice options to hide, per entity/field. |

# PBI Traceability and Update Notes

| PBI | Last Updated | Last Updated By | Comments |
| --- | --- | --- | --- |
| [#PBI No.] | 16/September/2025 | [Name] | Initial documentation of `Hide Choice Options.js` |
