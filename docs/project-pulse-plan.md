# Project Pulse implementation plan

## Scope and goals

Build a lightweight static Project Pulse dashboard for contributors. It must present multiple projects with each project’s name, owner, status, recent activity, priority/risk, and a concise contributor-friendly summary. Deliver a polished, responsive, accessible card dashboard and a VS Code launch configuration named **Run Project Pulse Dashboard** that serves `app/` and opens `index.html`.

## Current-state findings

- `app/` is empty; no framework, package manager, or existing UI patterns exist.
- Required deliverables are `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
- The exercise workflow specifically expects `docs/project-pulse-plan.md`.
- The Coder agent requires strict JSON, `${workspaceFolder}/app` as launch `cwd`, and an `index.html` browser target.
- The implementation should use browser-native HTML, CSS, and JavaScript only.

## Architecture, data, and UI behavior

- `app/project-data.json`: a top-level `projects` array. Every item includes `name`, `owner`, `status`, `recentActivity`, and `priority`; add `summary` to fulfill the contributor-summary requirement.
- `app/index.html`: semantic dashboard shell plus embedded JavaScript that fetches `project-data.json`, validates the response, and renders `.project-card` elements safely with DOM APIs/text content.
- `app/styles.css`: visual tokens and responsive styles for `.dashboard`, cards, badges, metadata, loading/error states, and priority/status variants.
- `.vscode/launch.json`: strict JSON configuration named **Run Project Pulse Dashboard**. Run `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`, then use `serverReadyAction` to open `http://localhost:%s/index.html`.

## Visual, UX, accessibility, and responsiveness

- Use a clear page title and short purpose statement, followed by a responsive project-card grid.
- Cards prioritize project name, status badge, owner, summary, recent activity, and priority/risk treatment.
- Use rounded cards, restrained shadows, high-contrast color pairings, readable spacing, and status/priority labels that do not rely on color alone.
- Use semantic `main`, headings, list/card semantics, and descriptive text labels.
- Support narrow screens with a single-column layout; expand to multi-column cards when space permits.
- Respect keyboard navigation and visible focus indicators. Avoid interactions requiring hover.

## Ordered implementation steps and file ownership

1. **Designer — visual specification**
   - Files: design guidance only; no implementation-file ownership.
   - Define hierarchy, badge labels, status/priority color mapping, breakpoints, loading/error presentation, and accessibility requirements.
   - Dependency: none.

2. **Coder — project content**
   - File: `app/project-data.json`
   - Add several representative projects with all required fields and concise `summary` values.
   - Dependency: Designer’s content hierarchy and controlled status/priority vocabulary.

3. **Coder — dashboard markup and rendering**
   - File: `app/index.html`
   - Create the Project Pulse shell, link `styles.css`, fetch `project-data.json`, render visible `.project-card` cards, and expose loading, empty, and error states.
   - Dependency: data schema from Step 2.

4. **Coder — styling**
   - File: `app/styles.css`
   - Implement `.dashboard`, `.project-card`, responsive grid, badges, metadata hierarchy, `border-radius`, `box-shadow`, focus styles, and state styling.
   - Dependency: Designer guidance; may begin in parallel with Step 3 after shared class names are agreed.

5. **Coder — runnable preview**
   - File: `.vscode/launch.json`
   - Add the strict-JSON launch configuration and direct browser target.
   - Dependency: none beyond the required app directory and port convention.

6. **Orchestrator — integration review**
   - Files: review all four implementation files.
   - Confirm rendered fields, visual consistency, graceful fetch failures, and that the launch configuration opens the dashboard rather than a directory listing.
   - Dependency: Steps 2–5 complete.

## Dependencies and parallel work

- Step 1 precedes the final implementation decisions for Steps 2–4.
- Data must establish the field vocabulary before HTML rendering is finalized.
- `app/project-data.json` and `.vscode/launch.json` can be implemented in parallel.
- Once class names and semantics are agreed, `app/styles.css` can proceed in parallel with the final HTML renderer.
- Integration validation is sequential and follows all implementation work.

## Edge cases and risks

- A `fetch()` request needs an HTTP server; opening `index.html` directly may fail due to browser file-origin restrictions.
- Handle missing/unreadable JSON, non-OK HTTP responses, invalid JSON, a missing `projects` array, an empty array, and incomplete project fields.
- Use fallback text for optional/invalid values and avoid injecting data via `innerHTML`.
- Keep status/priority values controlled or normalize them before assigning CSS modifier classes.
- The brief requires a summary but does not list `summary` among mandatory fields; add it as an allowed extra field.
- Confirm `serverReadyAction` matches Python’s server output in the target VS Code environment.

## Testing and validation

- Parse `app/project-data.json` and `.vscode/launch.json` with `python3 -m json.tool`.
- Verify `index.html` contains `Project Pulse`, references `styles.css` and `project-data.json`, and renders `project-card`, status, recentActivity, and priority content.
- Verify CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- Start **Run Project Pulse Dashboard** from Run and Debug; confirm it serves from `app/` and opens `http://localhost:5500/index.html`.
- Test normal data, empty data, and failed/invalid data behavior; inspect desktop and narrow viewport layouts, keyboard focus, readable contrast, and browser console errors.

## Open assumptions

- Project content, owners, activity dates, and risk vocabulary are illustrative unless Mona supplies authoritative data.
- No filtering, sorting, persistence, authentication, or backend API is in scope.
- Browser support targets current Codespaces/VS Code preview browsers.
