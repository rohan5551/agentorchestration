# Project Pulse dashboard design specification

## Purpose

Project Pulse gives contributors an at-a-glance view of project health, progress,
ownership, current work, and the next meaningful deadline. The dashboard should
let a contributor answer these questions without opening a project:

1. What needs attention now?
2. Is this project healthy and how far along is it?
3. Who owns it?
4. What changed recently and what is due next?

This is a read-only, static dashboard. Filtering, sorting, and drill-down views
are out of scope.

## Page structure and information hierarchy

Use a semantic `main.dashboard` page shell with a compact, full-width header and
a responsive card grid. Do not make the header sticky; preserving vertical space
is more valuable than persistent controls in this small, scan-oriented view.

### 1. Dashboard header

Place this content first, aligned to the same maximum-width container as the
cards:

- **H1:** `Project Pulse`
- **Purpose statement:** “A quick view of contributor projects, health, and
  upcoming work.”
- **Project count:** e.g., “6 active projects,” visually secondary but exposed
  as normal text.

The title establishes the product; the short description clarifies the data;
the count confirms that the load succeeded and helps users assess coverage.

### 2. Health summary strip

Directly below the header, show four compact, non-interactive summary items:

- **Healthy** — count
- **Needs attention** — count
- **At risk** — count
- **Milestones this week** — count

This strip makes exceptions visible before users scan individual cards. Each
item includes an icon only as a supplement and a text label/count. Use a
`role="list"` with list items, rather than buttons, because the initial release
does not support filtering.

### 3. Project section

Use an `h2`, “Active projects,” followed by a `ul.project-grid` (or equivalent
list semantics). Render each project as an `li` containing an `article.project-card`.
The card order is:

1. **Project name** (`h3`) and status badge.
2. **Summary** — one or two short lines describing the contributor outcome.
3. **Health and progress** — health label plus a determinate progress bar and
   explicit percent, such as “68% complete.”
4. **Ownership** — avatar initial and `Owner: Name`.
5. **Current activity** — labeled “Latest activity,” with relative or absolute
   date when available.
6. **Priority/risk** — a clear label such as “Priority: High” or “Risk: Blocked.”
7. **Upcoming milestone** — calendar icon (supplementary), milestone name, and
   due date. This is the final content block so upcoming action is easy to scan
   across cards.

The supplied data plan guarantees name, owner, status, recent activity,
priority, and summary. Add `health`, `progress`, and `nextMilestone` to the
display model when data is available. If absent, use defined unavailable states
below rather than inventing a value.

## Project card anatomy

Cards must remain readable as independent units and should not require hover to
reveal important information.

| Area | Content and treatment |
| --- | --- |
| Header row | Project name on the left; status badge on the right. Allow the badge to wrap below the name on very narrow screens. |
| Summary | 15–24 words where possible; muted but comfortably readable body text. Clamp visually at two lines only if the full summary remains available in the DOM. |
| Progress | `Health: On track` (or equivalent) followed by a native `progress` element or an ARIA-equivalent bar. Always pair the visual bar with “NN% complete.” |
| Metadata | Owner and latest activity in a two-column definition/list layout on wider cards; stack on narrow cards. Use labels, not icon-only metadata. |
| Attention row | Priority/risk uses a compact pill. Make blocked or high-risk treatment visually stronger than normal priority. |
| Milestone | Separate from metadata with a top border or subtle tinted surface; lead with “Next milestone” and include a human-readable date. |

Use a card footer only for the milestone; do not add nonfunctional “View
project” controls. If a later release introduces project navigation, the whole
card may become a single link, provided nested interactive controls are avoided.

## Controlled vocabulary and visual states

Normalize incoming values before assigning modifier classes. Recommended hooks:
`.status--active`, `.status--paused`, `.status--complete`,
`.health--on-track`, `.health--attention`, `.health--at-risk`,
`.priority--low`, `.priority--medium`, `.priority--high`, and
`.priority--blocked`.

| State | Visible label | Meaning | Treatment |
| --- | --- | --- | --- |
| Active | Active | Work is underway | Blue/indigo neutral-status badge |
| Paused | Paused | Work is intentionally not moving | Gray badge |
| Complete | Complete | Delivery is finished | Green badge |
| On track | On track | No known material concern | Green health label and calm progress fill |
| Needs attention | Needs attention | An owner should review soon | Amber health label and progress fill |
| At risk | At risk | Delivery, scope, or dependency is threatened | Red health label and progress fill |
| Low priority | Priority: Low | Can wait | Neutral pill |
| Medium priority | Priority: Medium | Planned work | Blue pill |
| High priority | Priority: High | Needs timely action | Amber pill |
| Blocked | Risk: Blocked | Cannot proceed without resolution | Red pill plus explicit “Blocked” text |

Color must never be the sole signal. Every badge/pill carries its complete text
label, and the highest-severity states also use a stronger border or icon.

### Data and rendering states

- **Loading:** show 3–6 skeleton cards matching card dimensions. Set the card
  region `aria-busy="true"` and provide a visually available “Loading projects…”
  message; do not rely on skeletons alone.
- **Empty:** replace the grid with an `h2`/message: “No active projects yet.”
  Add supporting copy: “Projects will appear here when they are added.”
- **Error:** use a high-contrast, non-dismissable alert panel: “Projects could
  not be loaded. Refresh the page to try again.” Keep the page header visible.
- **Incomplete project:** still render the project name and supplied fields.
  Use “Health not reported,” “Progress not reported,” or “No upcoming milestone”
  for missing optional data. Do not use warning colors solely because data is
  unavailable.

## Responsive behavior

The layout is content-led rather than device-led:

- **0–599 px:** page gutters 16 px; one card per row; metadata stacks; badge may
  move below the title; health summary is a 2 × 2 grid. Maintain 44 × 44 px
  minimum targets if links are added later.
- **600–899 px:** 24 px gutters; two equal card columns when each card can stay
  at least 280 px wide; summary strip uses four compact columns where space
  permits.
- **900 px and above:** center content in a max-width of about 1200 px; use
  `repeat(auto-fit, minmax(280px, 1fr))` so the grid naturally uses 2–4 columns
  without overly wide cards.

Use CSS grid for cards and summary items, with `gap` rather than margin-based
spacing. Cards in a row may have differing heights; do not force equal content
heights if it creates empty visual space. Ensure text wraps rather than
overflowing, especially owner and milestone names.

## Visual direction

The interface should feel like a focused contributor workspace: calm, credible,
and quick to scan rather than decorative.

- **Surface:** very light cool-gray page background with white card surfaces.
- **Brand/accent:** deep indigo or blue for headings, focus, and standard
  active states; reserve amber and red exclusively for attention/severity.
- **Typography:** system sans-serif stack; 28–32 px bold page title, 18–20 px
  card names, and 14–16 px body/metadata. Use 1.4–1.6 line height for copy.
- **Spacing:** 8 px base rhythm; cards use 20–24 px internal padding and
  16–24 px gaps. Make the card header and milestone row visually distinct.
- **Shape/elevation:** 12–16 px corner radius, 1 px low-contrast border, and a
  restrained shadow. On hover-capable devices, a slight shadow/lift may
  acknowledge linked cards only; it is not an information-bearing state.
- **Status treatment:** badges/pills are compact, rounded, and medium-weight.
  Use dark text on light tinted backgrounds or white text on sufficiently dark
  backgrounds, verifying contrast in both cases.

Required CSS hooks include `.dashboard`, `.project-grid`, `.project-card`,
`.status-badge`, `.health`, `.progress`, `.project-meta`, `.priority-badge`,
`.milestone`, `.loading-state`, `.empty-state`, and `.error-state`.

## Accessibility requirements

- Use one page `h1`, then logical `h2` and `h3` levels. Do not skip headings.
- Keep all text and meaningful controls at WCAG 2.1 AA contrast: 4.5:1 for
  normal text and 3:1 for large text and non-text visual boundaries/states.
- Use semantic list/article structure and real text labels for status, owner,
  priority, activity, and milestone. Decorative icons need `aria-hidden="true"`.
- Represent progress with native `<progress value="68" max="100">68%</progress>`
  when possible. If a custom element is necessary, provide `role="progressbar"`
  with `aria-valuemin`, `aria-valuemax`, `aria-valuenow`, and an accessible name.
- Render activity and milestone dates with a `<time datetime="YYYY-MM-DD">`
  element. Use unambiguous date text (for example, “Nov 14, 2026”).
- Preserve visible keyboard focus using a 2–3 px high-contrast outline with an
  offset. Never remove outlines without a stronger replacement.
- Do not convey required content only on hover, only with color, or only inside
  a tooltip. Respect `prefers-reduced-motion` by removing card lift and
  skeleton shimmer transitions.
- Announce initial load completion, empty state, and failures with an
  appropriate `aria-live="polite"` status region; use `role="alert"` for the
  fetch failure.

## Implementation acceptance checklist

- The first viewport clearly identifies Project Pulse and surfaces health
  exceptions before card details.
- Every project card visibly includes name, status, summary, owner, recent
  activity, priority/risk, health, progress, and next milestone or its defined
  unavailable text.
- `.dashboard` and `.project-card` are present and the card grid adapts from
  one column to multiple columns without horizontal scrolling.
- Loading, empty, error, and incomplete-data states are intentional, readable,
  and accessible.
- Status, health, and priority are understandable in grayscale and by screen
  reader output alone.
