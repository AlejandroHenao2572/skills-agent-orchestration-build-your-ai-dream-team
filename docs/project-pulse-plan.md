# Project Pulse implementation plan

## Summary

Project Pulse is a small, dependency-free static dashboard for Mona's
contributors. It should make the team's current work scannable at a glance:
which projects are active, who owns them, their status, recent activity, and
priority or risk. The result is a polished, accessible, responsive card
interface rather than a directory listing.

The implementation follows the repository's four-agent workflow. The
Orchestrator owns sequencing and integration, the Planner (this plan) owns
phases and file ownership, the Designer owns the experience and styling
direction, and the Coder owns the implementation and runnable-app
configuration. The app has no build step or third-party dependency.

## Required outcome and conventions

- The visible page title must be exactly **Project Pulse**.
- `app/index.html` must reference `styles.css` and `project-data.json`, and
  render a card for every entry in the data.
- `app/project-data.json` must be valid JSON with a top-level `projects` array.
  Every project object must contain `name`, `owner`, `status`,
  `recentActivity`, and `priority`.
- Project cards must use the `project-card` class. Status, priority, and
  recent activity must be visible, not only present in hidden data or
  attributes.
- `app/styles.css` must provide deterministic `.dashboard` and
  `.project-card` hooks, including rounded cards (`border-radius`) and depth
  (`box-shadow`), with readable spacing and responsive behavior.
- `.vscode/launch.json` must be strict JSON and define **Run Project Pulse
  Dashboard**, serve `${workspaceFolder}/app` with `python3 -m http.server
  5500`, and open `http://localhost:%s/index.html`.
- The launch target must be `index.html`, so the preview opens the dashboard
  instead of the server's directory listing.

## Ordered implementation steps

### 1. Confirm context and establish ownership

**Owner:** Orchestrator, informed by Planner
**Files:** read-only repository context; no implementation changes

Read the brief, `docs/agent-team.md`, all custom agent definitions, the four
step documents, `.vscode/tasks.json`, and `scripts/validate-exercise.sh`.
Confirm the required paths and checks before delegation. Keep each output file
assigned to one implementation owner to avoid conflicting edits.

### 2. Produce the information architecture and design direction

**Owner:** Designer
**Assigned file:** `app/styles.css`
**Related decisions for:** `app/index.html`

Define the first-view hierarchy: a heading and short purpose statement,
followed by a responsive project grid. Specify accessible labels and heading
levels, sufficient color contrast, a non-color-only status/priority treatment,
focus-visible behavior, readable line lengths, and a mobile-to-desktop layout.
Choose a restrained visual system with clear status badges, priority/risk
emphasis, rounded cards, shadows, and consistent spacing. Implement that
direction in `app/styles.css`, including `.dashboard` and `.project-card`.

The Designer should not alter the data schema or launch configuration. The
Designer may give markup guidance to the Coder, but the Coder remains the sole
editor of `app/index.html` in the implementation phase.

### 3. Define representative deterministic project data

**Owner:** Coder
**Assigned file:** `app/project-data.json`

Create valid, contributor-friendly sample records in a top-level `projects`
array. Include multiple projects so the grid and responsive behavior are
meaningful. Every record must have non-empty, human-readable `name`, `owner`,
`status`, `recentActivity`, and `priority` values. Use a small, stable set of
status and priority labels that can be styled consistently; do not rely on
timestamps, random values, or an API.

### 4. Implement the semantic dashboard shell and rendering

**Owner:** Coder
**Assigned file:** `app/index.html`
**Depends on:** Steps 2 and 3

Build a semantic document with the exact visible title **Project Pulse**, a
linked stylesheet, and a script/reference that loads `project-data.json`.
Render the top-level `projects` array into one `.project-card` per project.
Each card must visibly expose the project name, owner, status,
`recentActivity`, and priority. Use meaningful headings, lists or definition
groups as appropriate, accessible badge text, and an explicit loading and
error state for data fetch failures. Keep the page usable without a framework.

The Coder should escape or safely insert data values before placing them in
the DOM, handle an absent or malformed `projects` array with a clear message,
and avoid making the dashboard appear complete when data did not load.

### 5. Add the deterministic VS Code launch configuration

**Owner:** Coder
**Assigned file:** `.vscode/launch.json`
**Depends on:** Steps 1 and 4

Create strict JSON with no comments. Add a launch configuration named
**Run Project Pulse Dashboard** whose server command is exactly
`python3 -m http.server 5500`, whose `cwd` is exactly
`${workspaceFolder}/app`, and whose `serverReadyAction` opens
`http://localhost:%s/index.html`. Configure the launch so the browser opens
the page target and not the directory root. Keep the existing
`.vscode/tasks.json` folder-open task unchanged; it is the Copilot CLI exercise
terminal and is not the dashboard launch configuration.

### 6. Integrate and review the four outputs

**Owner:** Orchestrator with Coder and Designer
**Depends on:** Steps 2–5

Review the assembled HTML against the Designer's hierarchy and responsive
hooks, check that data keys and rendered fields agree exactly, and check the
launch working directory/URL. Resolve any integration issue in the owning
file only. No agent should stage, commit, or push changes.

### 7. Validate and hand off

**Owner:** Orchestrator
**Depends on:** Step 6

Run the static checks below, perform the runtime smoke test, record failures
with actionable messages, and report the files and agents involved. Stop the
preview server after the smoke test. The final handoff should identify any
remaining limitation, such as the dashboard being static and using local
sample data.

## Explicit file assignments

| File | Primary owner | Scope and acceptance |
| --- | --- | --- |
| `app/index.html` | Coder | Exact visible `Project Pulse` title; stylesheet and JSON references; semantic shell; visible `.project-card` elements; visible status, `recentActivity`, and priority; loading/error handling. |
| `app/styles.css` | Designer | Polished responsive visual system; `.dashboard` and `.project-card`; readable spacing; accessible contrast/focus; `border-radius` and `box-shadow`; status and priority treatments. |
| `app/project-data.json` | Coder | Valid JSON; top-level `projects` array; multiple stable records; each record has `name`, `owner`, `status`, `recentActivity`, and `priority`. |
| `.vscode/launch.json` | Coder | Strict JSON; `Run Project Pulse Dashboard`; `cwd` `${workspaceFolder}/app`; command `python3 -m http.server 5500`; server-ready URL `http://localhost:%s/index.html`. |

The Orchestrator may coordinate reviews but should not silently reassign these
file scopes. The existing `.vscode/tasks.json` is context only and must not be
changed for this dashboard.

## Designer responsibilities

The Designer is responsible for:

1. Establishing a contributor-first information hierarchy and a quick-scan
   card layout.
2. Implementing the visual styling in `app/styles.css`, including the
   deterministic `.dashboard` and `.project-card` hooks.
3. Defining readable typography, spacing, contrast, rounded card surfaces,
   shadows, status badges, and priority/risk affordances.
4. Making the grid responsive and usable at narrow widths without horizontal
   scrolling.
5. Specifying semantic and accessibility expectations: logical headings,
   visible labels, keyboard focus, reduced-motion-friendly transitions, and
   status/priority cues that do not depend on color alone.
6. Reviewing the integrated HTML visually and identifying design regressions;
   the Designer does not edit Coder-owned data or launch files.

## Coder responsibilities

The Coder is responsible for:

1. Creating deterministic sample data in `app/project-data.json` with the
   required schema and a top-level `projects` array.
2. Implementing `app/index.html` to load the data and render all project
   records as visible `.project-card` elements with every required field.
3. Linking the CSS and JSON resources using paths that work from the `app`
   server root.
4. Providing clear loading, empty, malformed-data, and fetch-error states and
   safely rendering data values.
5. Creating `.vscode/launch.json` as strict JSON with the exact server,
   working directory, name, and `index.html` server-ready URL.
6. Running syntax, schema, and browser/server smoke checks, and reporting
   remaining risk without staging, committing, or pushing.

## Dependencies and work ordering

The data schema (Step 3) and Designer's CSS hooks (Step 2) are independent
inputs and can be produced **in parallel** after Step 1, because they use
different files. The Coder can also prepare the launch configuration in
parallel with the initial data and CSS work, provided the required app
directory and exact launch values are known.

The HTML implementation (Step 4) must be **sequential after** the design
contract and data schema: it needs the `.dashboard`/`.project-card` contract
and the exact JSON field names to render consistently. It may be developed
against a documented provisional schema, but integration review must wait for
the final data file.

Launch configuration work can be parallel with Steps 2–4 at the file level,
but its end-to-end test must be sequential after the app files exist. Full
integration review and runtime validation must be sequential after all four
assigned outputs are present. Any fix discovered by validation returns to the
owning agent, followed by the relevant targeted check and then the full
validation pass.

## Edge cases and risks

- **Missing or invalid JSON:** A fetch failure, invalid JSON, or missing
  `projects` array must show an understandable in-page error; an empty array
  should show an intentional empty state rather than a blank page.
- **Unexpected field values:** Missing or unusually long text should not break
  the layout. Cards should wrap text, and status/priority styling should have a
  readable fallback for unknown labels.
- **Unsafe content:** Render data as text or safely escape it; do not inject
  untrusted values as raw HTML.
- **File URL versus HTTP:** `fetch` may fail when `index.html` is opened
  directly with a `file://` URL. Validate through the Python server and launch
  configuration, not only by double-clicking the file.
- **Directory listing regression:** A `cwd` of the repository root, a root
  server-ready URL, or an omitted `index.html` can open a listing. Verify both
  the exact `cwd` and URL.
- **Port collision:** Port 5500 may already be occupied. Report the collision
  and stop the competing process safely; do not silently change the prescribed
  port.
- **Accessibility regressions:** Color-only badges, low contrast, missing
  focus styles, heading jumps, and non-wrapping text are release blockers for
  the UI.
- **Responsive overflow:** Long activity strings or many cards must remain
  readable on small screens without horizontal scrolling.
- **Static-data expectation:** There is no backend, authentication, live
  refresh, or persistence in this exercise; document that sample data is
  local and deterministic.

## Validation expectations

### Static validation

Before handoff, verify:

1. `app/index.html`, `app/styles.css`, `app/project-data.json`, and
   `.vscode/launch.json` exist.
2. `python3 -m json.tool app/project-data.json` succeeds.
3. `python3 -m json.tool .vscode/launch.json` succeeds and the file has no
   comments.
4. The HTML contains the exact visible title `Project Pulse`, references
   `styles.css` and `project-data.json`, and contains `project-card`,
   `status`, `recentActivity`, and `priority` rendering paths.
5. The CSS contains `.dashboard`, `.project-card`, `border-radius`, and
   `box-shadow`, plus responsive rules and visible focus/contrast treatment.
6. A schema check confirms `projects` is an array, has multiple records, and
   each record contains non-empty `name`, `owner`, `status`,
   `recentActivity`, and `priority`.
7. The launch JSON contains the exact name, command, `cwd`, and
   `http://localhost:%s/index.html` target required above.
8. The repository's `scripts/validate-exercise.sh` still passes for the
   exercise-level checks. That script also validates existing workflow,
   devcontainer, task, and agent conventions; it is not a substitute for the
   app schema and browser checks above.

### Runtime validation

Start the prescribed server from `app/` with
`python3 -m http.server 5500`, or use **Run Project Pulse Dashboard** in VS
Code. Confirm that the server-ready browser URL is
`http://localhost:5500/index.html`, not `/`, and that the rendered page shows
the exact title, multiple project cards, each owner, status badge, recent
activity, and priority. Inspect the browser console for failed resource loads
or uncaught errors. Resize to a narrow viewport and verify readable cards,
wrapping, no horizontal overflow, keyboard focus, and sufficient contrast.
Also test a temporarily unavailable or malformed data response if practical to
confirm the error state. Stop the server after validation.

## Open questions and working decisions

- **Data source:** Working decision is local JSON only; no API or build
  tooling is needed for this exercise.
- **Interaction scope:** Working decision is read-only cards. Filtering,
  sorting, editing, authentication, and live updates are intentionally out of
  scope unless a later requirement is added.
- **Status vocabulary:** Working decision is a small, documented set of
  contributor-friendly labels with a safe visual fallback for unknown values.
  The exact sample labels may be selected by the Coder as long as all values
  remain visible and meaningful.
- **Browser target:** Working decision is modern evergreen browsers available
  in the Codespace/VS Code preview; no framework-specific polyfills are
  required.
- **Launch browser choice:** The launch configuration should use the
  repository's expected VS Code browser/server-ready mechanism while keeping
  the required command, `cwd`, name, and URL exact. If the environment lacks a
  browser handler, the HTTP smoke test remains the fallback validation.
