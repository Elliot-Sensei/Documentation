[[*TOC*]]

# Overview / Summary

The **Hide Choice Options** configuration setting is the rule set that tells the web resource which Choice field options to remove for a given entity. It is stored in the **Sensei Config Settings** table as the record whose logical name is `HideChoiceConfig` and is read at form load by the `Hide Choice Options.js` script.

The configuration is structured around `entitiesTable`, which contains one or more entity entries. Each entity entry then contains `choiceColumns`, which defines the target field and the values to hide. This design keeps the business rules outside the JavaScript code, so new columns or option values can be controlled by configuration without changing the script itself.

![image.png](/.attachments/image-a2d1e3be-1d12-4590-8df9-733674a44750.png)

# Backlog Item/s

- #31158
- #33792

# Related Item/s

- [Hide Choice Fields - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/13437/Hide-Choice-Fields)
- [Change Requests - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/12210/Change-Requests)

# Links

N/A

# Navigating to the Changes

Config Setting: Navigate to Power Apps ➡️ Tables ➡️ **Sensei Config Settings** (`se_senseiconfigsettings`) ➡️ **Hide Choice Config**

Web Resource: Navigate to Power Apps ➡️ Solutions ➡️ **Sensei Base** ➡️ Web Resources ➡️ **Hide Choice Options.js**

# Configuration Setting Design

## Record and table

The setting is stored in the `se_senseiconfigsettings` table and is keyed by `se_logicalname` equal to `HideChoiceConfig`.

The script reads:

- `se_logicalname` to find the correct config record
- `se_value` to retrieve the JSON payload

The payload is expected to be JSON in the following structure:

- `entitiesTable` is the main collection of rules
- each item in `entitiesTable` has an `entity` object
- each item also has a `choiceColumns` array
- each item in `choiceColumns` defines one hide rule for one Choice field

## Entity level

Each `entity` entry contains:

- `value`: the logical name of the Dataverse table, such as `sensei_changerequest`
- `label`: the friendly display name for the table, such as `Change Request`

This matches the current form's entity before applying any rules. If the entity name does not match, the script ignores the group.

## Choice column rule

Each rule in `choiceColumns` contains the following fields:

### `targetAttributeColumn`

This is the target column or attribute logical name, such as `sensei_category`.

It is represented as an object with:

- `value`: the logical name used by the script
- `label`: the display name shown in the configuration UI

This is the exact field whose options should be adjusted.

### `matchMode`

The match mode decides how the values in `targetValues` are interpreted.

- `value`: match by the numeric option set value
- `label`: match by the visible option label text

The default is `value`.

### `applyTo`

This controls where the option hiding is applied.

- `both`: apply to the main form body and the header control
- `body`: apply only to the main form control
- `header`: apply only to the header control

The default is `both`.

### `hideOption`

This is a boolean flag that turns the rule on or off.

- `true`: hide the configured options
- `false`: skip the rule

This is useful when a rule is intentionally disabled without deleting it from the config.

### `targetValues`

This is the list of option values or labels to hide.

Each entry is an object with a `choiceOptions` field, which contains the value or text to match in the selected mode.

Examples:

- When `matchMode` is `value`, the values are numeric option set values, such as `955000000`
- When `matchMode` is `label`, the values are visible labels, such as `Approved` or `Closed`

# PBI Traceability and Update Notes

| PBI | Last Updated | Last Updated By | Comments |
| --- | --- | --- | --- |
| #31158, #33792 | 16/September/2025 | E Avery | Initial documentation of the Hide Choice Options config setting |
