# Project Pulse dashboard implementation plan

## Summary

Build Mona’s Project Pulse as a small static dashboard that helps contributors see project names, owners, status, recent activity, priorities or risks, and concise summaries. The repository has no existing app implementation or app build/test setup; the brief and exercise steps define the requirements. Prefer plain HTML, CSS, JSON, and browser APIs—no framework or package installation is needed.

The Orchestrator should coordinate Planner, Designer, and Coder using explicit file scopes. Designer owns visual and accessibility decisions and `app/styles.css`. Coder owns the HTML/data implementation and VS Code launch configuration. The Orchestrator integrates and verifies the result without taking over implementation.

## Ordered implementation steps

### 1. Agree on the interface and data contract

- **Designer:** Define a brief visual/interaction contract: information order, card anatomy, responsive behavior, accessible status and priority treatments, and the shared CSS/HTML hooks `.dashboard` and `.project-card`.
- **Coder:** Confirm the data contract from the brief before implementation: top-level `projects` array; each project has `name`, `owner`, `status`, `recentActivity`, and `priority`. Agree on a small, consistent set of readable status and priority labels.
- **Files:** No files for Designer’s design direction; Coder owns `app/project-data.json`.
- **Dependencies:** The field names are fixed by `.github/project-pulse-brief.md`. Keep this contract short and communicate it to both implementation owners.

### 2. Build the dashboard and launch preview

Once the shared hooks and data contract are clear, these tasks can run in parallel because file ownership does not overlap:

- **Designer — `app/styles.css`:** Implement a polished, responsive card layout with clear hierarchy, readable spacing and type, sufficient contrast, and visible status/priority treatments. Include `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`. Preserve text labels so meaning is not conveyed by color alone.
- **Coder — `app/index.html`:** Create the semantic page with the exact title “Project Pulse”; reference `styles.css` and `project-data.json`; render visible project cards and the required fields. Since only the four assigned files are in scope, keep any data-loading/rendering JavaScript inline in this HTML rather than adding a separate script file. Show understandable loading, empty, and data-load-error states.
- **Coder — `app/project-data.json`:** Provide a valid, non-empty sample dataset with the required top-level array and fields on every project. A concise optional `summary` field is reasonable if it improves the contributor-friendly overview, but it must not replace or rename required fields.
- **Coder — `.vscode/launch.json`:** Add strict JSON with the configuration name **Run Project Pulse Dashboard**. Follow the exercise requirements: run `python3 -m http.server 5500` with the working directory `${workspaceFolder}/app`, and use `serverReadyAction` to open `http://localhost:%s/index.html` rather than the directory root. Choose a VS Code launch type that actually starts this command in the Codespace; the repository’s devcontainer does not list the Python extension among its installed extensions, so verify the chosen mechanism instead of assuming it is available.

### 3. Integrate, review, and resolve issues

- **Orchestrator:** Review the four deliverables together against the design/data contract and exercise requirements.
- **Coder:** Fix HTML, data, or launch issues only in `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.
- **Designer:** Fix styling issues only in `app/styles.css`, based on review feedback.
- **Dependencies:** This phase follows Steps 1–2. Keep corrections sequential when they touch the same file; do not have Designer and Coder edit `app/styles.css` concurrently.

## Dependencies and parallel work

- The data field names, `.dashboard`/`.project-card` hooks, and agreed card structure are the shared contract for implementation.
- The initial design direction and JSON dataset can be developed in parallel: the brief already defines the required data fields.
- After the shared contract is set, Designer’s stylesheet task and Coder’s HTML/data/launch tasks can run in parallel because their file assignments are distinct.
- Integration testing must wait until all four files exist.
- Any fixes to a given file must be sequential and assigned to its owner. Orchestrator review follows integration; it does not edit implementation files.

## Edge cases and risks

- `fetch()` of `project-data.json` needs to run over HTTP; opening the page directly as `file://` may fail. Use the launch configuration for the preview.
- Handle a missing, invalid, or empty `projects` array with a clear user-facing state rather than a blank dashboard or uncaught error.
- Ensure every project has all required fields and that long names, activity text, and labels wrap without breaking the card layout.
- Use readable text and accessible markup; status and priority must remain understandable without color. Check keyboard/focus behavior if controls are added; avoid adding unnecessary interactions.
- Render JSON strings as text rather than interpreting them as HTML.
- Port `5500` may already be occupied. If the server does not start, identify the conflict and keep the configured port, launch behavior, and documentation consistent with the exercise requirements.
- The launch JSON must be strict JSON with no comments. A server readiness pattern that fails to match Python’s startup output can leave the browser unopened.
- Python’s `http.server` is suitable for this local exercise preview, not production hosting.

## Validation expectations

1. Confirm all four assigned files exist and that the page references `styles.css` and `project-data.json`.
2. Parse both JSON files:
   - `python3 -m json.tool app/project-data.json`
   - `python3 -m json.tool .vscode/launch.json`
3. Check the dataset has a top-level `projects` array and that each entry contains `name`, `owner`, `status`, `recentActivity`, and `priority`.
4. Check the page visibly renders multiple `.project-card` elements with status, recent activity, and priority. Check the CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
5. Run the repository’s Step 3 workflow checks where available; `.github/workflows/3-step.yml` defines the exercise’s expected file and keyphrase checks.
6. In the Codespace, select **Run Project Pulse Dashboard** and verify the server starts, the browser opens `/index.html`, the dashboard—not a directory listing—appears, and the JSON-backed cards render. Stop the server afterward.
7. Check a narrow viewport and a load-error or empty-data path. Confirm text remains legible and status/priority are not communicated by color alone.

## Open questions and assumptions

- **Launch mechanism:** Which launch type can execute `python3 -m http.server 5500` in this Codespace without relying on an uninstalled extension? Verify this during implementation; resolve it before calling the launch task complete.
- **Sample projects:** The brief does not specify real project names, owners, or statuses. Use clearly illustrative, fictional sample data unless Mona supplies approved content.
- **Summary field:** The brief asks for a contributor-friendly summary but does not list `summary` among required JSON keys. Treat it as an optional additional field, or make the required existing fields provide the concise overview.
- Assume this is a static local preview with no backend, persistent data, or production deployment requirement.
