[[_TOC_]]

# Overview / Summary

**Risk Guide.html** is an HTML web resource that renders the message displayed by the Dynamic Message web resource control. It reads its content from query-string parameters, converts Markdown-formatted text to sanitised HTML, and styles the message according to a configured message type.

The web resource is embedded as the source of a form web resource control. It is not called directly; the `Dynamic Message` web resource sets its `message`, `type`, and `showIcon` query-string parameters to control what is displayed.

# Backlog Item/s

- #31518.

# Related Item/s

- Dynamic Message - Overview.
- Dynamic Form Message - Overview.

# Links

- Risk Guide.HTML - Overview.

# Navigating to the Changes

Web Resource: Navigate to Power Apps -> Solutions -> **Sensei Base** -> Web Resources -> **Risk Guide.html**.

# Changes Implemented

## Web resource

| Property | Value |
| --- | --- |
| **Display Name / File** | Risk Guide.html |
| **Type** | Web Resource (HTML) |

The web resource loads the following external libraries:

- Fabric core and Fabric icons stylesheets, used for consistent styling and iconography with the Dataverse form.
- `marked`, used to convert the supplied Markdown message text into HTML.
- `DOMPurify`, used to sanitise the generated HTML before it is inserted into the page.

## Query-string parameters

The web resource reads the following parameters from its own URL:

| Parameter | Description |
| --- | --- |
| `message` | The URL-encoded message text, written in Markdown. |
| `type` | The message presentation type. Defaults to `info` when omitted. |
| `showIcon` | Whether the message icon is displayed. Defaults to `true` unless the value is `false`. |

## Message rendering

On page load, the web resource decodes the `message` parameter, converts it from Markdown to HTML using `marked` with GitHub-flavoured Markdown and line breaks enabled, and sanitises the result using `DOMPurify` before inserting it into the page. Sanitisation removes `script` and `style` tags and the `onerror`, `onload`, and `style` attributes, while explicitly allowing common formatting tags (including headings, tables, lists, images, code blocks, block quotes, and `iframe`) and their supporting attributes.

Any link with an `http` `href` has `target="_blank"` and `rel="noopener noreferrer"` applied so that message links open in a new tab without exposing the originating page.

If the message cannot be parsed, the web resource displays "Error displaying message" and switches to the error presentation style.

## Message presentation types

The `type` parameter selects both the container styling and the icon:

| Type | Background/border colour | Icon |
| --- | --- | --- |
| `success` | Green | `CompletedSolid` |
| `warning` | Yellow | `Warning` |
| `error` | Red | `ErrorBadge` |
| `info` | Blue | `Info` |

The icon is hidden when `showIcon` is `false`.

## Reporting

A before-and-after visual comparison is not applicable because this is a new, configuration-driven web resource. The rendered appearance depends on the `message`, `type`, and `showIcon` parameters supplied by the calling form web resource control.

# PBI Traceability and Update Notes

| PBI | Last Updated | Last Updated By | Comments |
| --- | --- | --- | --- |
| #31518 | 16/September/2026 | E Avery | Initial documentation of the Risk Guide.html web resource. |
