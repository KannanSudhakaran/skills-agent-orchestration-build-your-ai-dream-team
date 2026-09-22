# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's Project Pulse as a dependency-free static dashboard. The dashboard will be served locally with Python's built-in HTTP server, load project information from `project-data.json`, and render accessible, responsive project cards in the browser. It will not require a package manager, framework, build step, or external service.

The HTML will provide the dashboard structure and client-side JSON loading/rendering behavior. The CSS will establish the visual system and responsive card treatment. The data file will be the single content source. The dashboard container and individual cards must use `.dashboard` and `.project-card`, respectively, as stable selector hooks.

## Responsibilities and File Ownership

| Role | Owned file(s) | Responsibilities |
| --- | --- | --- |
| Designer | `app/styles.css` | Define the responsive visual design, typography, spacing, status/priority treatments, focus states, and card presentation. Preserve `.dashboard` and `.project-card` selector hooks. |
| Coder | `app/index.html` | Create accessible dashboard markup and the dependency-free JavaScript needed to fetch, validate at a basic level, and render JSON project records, including loading, error, and empty states. |
| Coder | `app/project-data.json` | Provide valid dashboard data using the agreed schema and representative values for all required fields. |
| Coder | `.vscode/launch.json` | Add the strict JSON, comment-free VS Code launch configuration for serving and opening the dashboard. |

## Data Contract

`app/project-data.json` must contain one top-level object with a `projects` array:

```json
{
  "projects": [
    {
      "name": "Project name",
      "owner": "Owner name",
      "status": "On track",
      "recentActivity": "Brief recent update",
      "priority": "High",
      "summary": "Short project description"
    }
  ]
}
```

Every project record must include these fields:

- `name`
- `owner`
- `status`
- `recentActivity`
- `priority`
- `summary`

The implementation should treat each value as display text, avoid injecting it as HTML, and render the record consistently even when values are long.

## Ordered Implementation Steps

1. **Confirm the implementation contract.**
   - Confirm the data schema, expected project status and priority values, visual direction, and supported browser expectations.
   - Agree that no third-party runtime dependencies, build tooling, or network APIs will be introduced.

2. **Create the data source (`app/project-data.json`).**
   - Add the top-level `projects` array.
   - Populate representative project records containing every required field.
   - Keep the file valid JSON: double-quoted property names and strings, no comments, and no trailing commas.

3. **Establish markup and rendering behavior (`app/index.html`).**
   - Add the document structure, page title, and a semantic dashboard heading.
   - Add a dashboard region using the `.dashboard` hook and an accessible loading/status area.
   - Fetch `project-data.json` using a relative path.
   - Render each valid project into an accessible card using the `.project-card` hook, with clear labels or semantic associations for owner, status, recent activity, priority, and summary.
   - Use safe DOM APIs such as `textContent` for JSON-derived values.
   - Implement distinct loading, fetch/parse/error, and empty-array states.
   - Ensure that errors are visible and understandable without relying only on color.

4. **Implement the presentation layer (`app/styles.css`).**
   - Style `.dashboard` as the responsive grid or layout container.
   - Style `.project-card` as a visually grouped, readable project summary with appropriate padding, borders/surface treatment, hierarchy, and status/priority affordances.
   - Provide responsive layouts that work on narrow mobile viewports, intermediate widths, and desktop widths.
   - Include visible keyboard-focus styling and sufficient color contrast.
   - Make long names, summaries, activity text, and owner names wrap rather than overflow.

5. **Add local launch support (`.vscode/launch.json`).**
   - Use strict JSON with no comments.
   - Add a configuration named `Run Project Pulse Dashboard`.
   - Configure it to run `python3 -m http.server 5500`.
   - Set `cwd` to `${workspaceFolder}/app`.
   - Configure `serverReadyAction` to open `http://localhost:%s/index.html`, so learners open the dashboard rather than a directory listing.

6. **Validate the integrated dashboard.**
   - Serve the `app` directory through the launch configuration or equivalent Python command.
   - Verify successful data loading and card rendering in a browser.
   - Exercise loading, empty, malformed/unavailable-data, narrow viewport, keyboard-navigation, and long-content scenarios.
   - Correct ownership-specific issues in the responsible file before handoff.

## Dependencies

| Work item | Depends on | Why |
| --- | --- | --- |
| Data file schema and sample records | Confirmed data contract | Rendering code needs stable field names and expected content. |
| HTML structure and rendering logic | Data contract | The renderer must read the top-level `projects` array and required fields correctly. |
| CSS card styling | Agreed selector and content contract | Styles require stable `.dashboard` and `.project-card` hooks and knowledge of card content. |
| Launch configuration | Final app directory and entry point | The server must use `app` as its working directory and open `index.html`. |
| Browser integration validation | Data, HTML, CSS, and launch configuration | End-to-end behavior cannot be confirmed until all implementation files exist. |

## Parallel Work Decisions

| Work that may proceed in parallel | Decision and rationale |
| --- | --- |
| `app/project-data.json` and the initial `app/index.html` structure | **Proceed in parallel** after agreeing to the schema. The file ownership is separate, and the schema supplies the integration contract. |
| `app/styles.css` and Coder's data/rendering implementation | **Proceed in parallel** after Coder communicates the stable `.dashboard` and `.project-card` hooks plus intended card fields. Designer can implement visual rules without waiting for final sample content. |
| `.vscode/launch.json` and dashboard implementation | **Proceed in parallel.** It is independent of the implementation details as long as the required `app/index.html` entry point and app directory are fixed. |
| Static JSON syntax checks and CSS review | **Proceed in parallel.** These are independent file-level checks before browser integration. |

## Sequential Work Decisions

| Work that must be sequential | Decision and rationale |
| --- | --- |
| Agree on the JSON schema before finalizing renderer logic | **Sequential.** The renderer depends on the exact `projects` container and six required field names. |
| Establish `.dashboard` and `.project-card` hooks before final CSS integration | **Sequential.** Designer must have stable hooks; changing them after styling risks disconnected or ineffective styles. |
| Finish data, markup/rendering, and CSS before end-to-end browser testing | **Sequential.** Full rendering, responsive behavior, and state treatment require the integrated set of files. |
| Run the configured server before validating serverReadyAction browser behavior | **Sequential.** The browser action depends on the server reaching its ready output and serving from the required working directory. |

## Edge Cases and Risks

- **Missing or malformed JSON:** Fetching, parsing, or shape validation can fail. Show a visible error message with a retry instruction where appropriate; do not leave a blank dashboard.
- **Empty project list:** When `projects` is an empty array, show a clear empty-state message rather than an empty grid.
- **Missing required record fields:** The renderer should handle invalid records predictably, such as reporting invalid data or using an explicit fallback only if that behavior is agreed upon. Do not silently mislabel values.
- **Local-file browser restrictions:** Opening `index.html` directly with `file://` may block `fetch`. Use the specified HTTP server for normal preview and validation.
- **Slow data response:** Keep a loading message visible until the request resolves or fails.
- **Long or unusual content:** Long names, summaries, activity text, and owner names must wrap; JSON content must be inserted as text, not HTML.
- **Status/priority communicated only by color:** Use text labels and, if color is added, pair it with text and adequate contrast.
- **Small screens and zoom:** Cards must remain readable, avoid horizontal scrolling, and retain usable spacing at narrow widths and browser zoom.
- **Keyboard and assistive-technology use:** Maintain semantic hierarchy, visible focus indicators, and an announced status/error region where needed.
- **Port 5500 already in use:** The configured preview cannot start until the conflicting process is stopped or an agreed alternate port is selected; the requested configuration itself must retain port 5500.

## Validation Expectations

### File and configuration checks

- Confirm these files exist: `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
- Parse `app/project-data.json` as JSON successfully.
- Confirm the data object has a top-level `projects` array.
- Confirm every project record has non-empty `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary` fields.
- Parse `.vscode/launch.json` as strict JSON and verify it contains no comments.
- Confirm the launch configuration is named `Run Project Pulse Dashboard`, runs `python3 -m http.server 5500`, uses `cwd` `${workspaceFolder}/app`, and has a `serverReadyAction` that opens `http://localhost:%s/index.html`.

### Structure and styling checks

- Confirm `app/index.html` includes `.dashboard` as the dashboard selector hook.
- Confirm every rendered project card includes `.project-card`.
- Confirm cards expose the project name and all required data fields in a readable, semantic order.
- Confirm CSS provides deliberate dashboard and card treatment rather than relying on unstyled default flow.
- Confirm focus states, contrast, text wrapping, and non-color status/priority labels are present.

### Runtime and browser checks

- Start the configured Python server and confirm it serves `index.html` from the `app` directory.
- Confirm the configured browser URL resolves to the dashboard, not a directory listing.
- Confirm valid JSON renders one card per project record with the correct values.
- Confirm the initial loading state is visible while data is pending.
- Confirm unavailable, malformed, or invalidly shaped data produces an understandable error state.
- Confirm an empty `projects` array produces an intentional empty state.
- Check responsive layouts at mobile, tablet, and desktop widths, including no unintended horizontal overflow.
- Check keyboard navigation and visible focus behavior in a supported browser.

## Open Questions

1. What exact set of `status` values should be used (for example, On track, At risk, Blocked), and should each have a prescribed visual treatment?
2. What exact set of `priority` values should be used, and should priority affect sorting or remain display-only?
3. Should cards appear in JSON source order, or should the dashboard sort or group projects by priority, status, or owner?
4. Is a project identifier, URL, due date, or last-updated timestamp needed in a future data schema?
5. What is the preferred visual direction for Mona's dashboard (brand colors, typography, density, and card elevation)?
6. Which browsers and minimum viewport sizes are required for acceptance?
7. Should the error state provide a manual retry control, or is a descriptive message sufficient for the first release?
