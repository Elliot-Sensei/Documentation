[[*TOC*]]

# Overview / Summary

The **DynamicFormMessage** configuration setting defines the conditions under which the Dynamic Message web resource is displayed on a form. It stores entity-specific column rules in the **Sensei Config Settings** table so that message content and visibility can be updated without changing the web resource code.

The setting is read from the `se_senseiconfigsettings` table by filtering `se_logicalname` for `DynamicFormMessage` and retrieving the `se_value` JSON payload.

![image.png](/.attachments/image-64e84d58-9697-4294-9e69-8fc19a4250ba.png)

# Backlog Item/s

- #31518.

# Related Item/s

# Links

- [Dynamic Message - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/13441/Dynamic-Message)
- [Risk Guide.HTML - Overview](https://dev.azure.com/SenseiCloud/SA%20Water/_wiki/wikis/SA-Water.wiki/13443/Risk-Guide.HTML)
- [Dynamic Form Message - Repos](https://dev.azure.com/SenseiCloud/_git/SA%20Water?path=/Sensei%20Config%20Settings/Dynamic%20Form%20Message)

# Navigating to the Changes

Config Setting: Navigate to Power Apps -> Tables -> **Sensei Config Settings** (`se_senseiconfigsettings`) -> **DynamicFormMessage**.

Web Resource: Navigate to Power Apps -> Solutions -> **Sensei Base** -> Web Resources -> **Dynamic Message**.

# Configuration Setting Design

## Record and table

The setting is stored in the `se_senseiconfigsettings` table with the following values:

| Column | Value or purpose |
| --- | --- |
| `se_logicalname` | `DynamicFormMessage`, used to identify the setting. |
| `se_value` | JSON containing the entity and column message rules. |

## Entity configuration

The JSON payload contains an `entityConfigurations` array. Each entry identifies the Dataverse table to which its rules apply through `entityLogicalName`.

The `entityLogicalName` value can be stored directly as a string or as an object with a `value` property. The web resource compares this value with the current form entity logical name and ignores configurations for other entities.

## Column configuration

Each entity configuration contains a `columnConfigurations` array. Each entry defines one condition and one message web resource.

| Property | Description |
| --- | --- |
| `logicalName` | The column logical name to evaluate. It can be a string or an object with a `value` property. |
| `columnValue` | The value used by the selected operator. |
| `operatorName` | The comparison operator. It defaults to `=` when omitted. |
| `webResourceName` | The name of the form web resource control to update. |
| `customMessage` | The message text passed to the web resource. |
| `shouldShowMessage` | Controls whether the web resource control is visible after a match. |
| `messageType` | The message presentation type. It defaults to `info` when omitted. |
| `shouldShowIcon` | Controls whether the message icon is displayed. It defaults to `true` when omitted. |

The web resource applies each unique combination of column, operator, comparison value, web resource, and visibility flag once. Duplicate rules with the same combination are ignored during processing.

## Supported column values

The comparison logic supports standard values and additional Dataverse column behaviours:

- Lookup columns can match the related record name or normalised identifier.
- Boolean columns can match the text values `Yes` and `No`.
- Choice columns can match the displayed option text.
- Date columns can be tested with `is in past` and `is in future`.
- Empty values can be tested with `contains data` and `does not contain data`.

## Configuration behaviour

When the form handler runs, it loads the setting, selects the configuration for the current entity, and evaluates each column rule. If a rule does not match, its web resource control is hidden. If it matches, the control source is rebuilt with the configured `message`, `type`, and `showIcon` parameters, and the control visibility is set from `shouldShowMessage`.

Invalid or incomplete rules are logged and skipped for the affected rule. A missing setting, entity configuration, column, or web resource control does not prevent unrelated form processing from completing.

# PBI Traceability and Update Notes

| PBI | Last Updated | Last Updated By | Comments |
| --- | --- | --- | --- |
| #31518 | 16/September/2026 | E Avery | Initial documentation of the DynamicFormMessage config setting. |
