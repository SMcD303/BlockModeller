# Programme Modeller

A single-file HTML planning tool for scheduling projects across a team, visualising capacity, and managing pipeline distribution. No build step, no server, no dependencies — open the file in a browser and start planning.

---

## Features

### Scheduling
- Priority-based scheduling engine that respects **project dependencies** (blocking relationships)
- Per-project **earliest start** constraints
- Projects are assigned to team members based on **role effort splits** (e.g. Developer 60% / Designer 40%)
- Overflow detection — projects that can't be delivered within the horizon are flagged

### Team & Capacity
- Add team members with a **name**, **role**, **base velocity** (story points per month), and optional **max capacity ceiling**
- **Inline editing** for team members — click Edit on any member card to update their details in place
- **Velocity growth rate** — a compound % increase per month applied to each member's capacity, modelling ramp-up or improving throughput over time. The max capacity ceiling caps growth at a realistic limit
- Per-member monthly capacity bars with project-level colour coding, including a visual marker where growth hits the ceiling

### Projects
- Add projects manually via the sidebar form or import in bulk via CSV
- **Inline editing** for projects — click Edit on any project card to update name, pipeline, priority, earliest start, colour, role splits, and dependencies in place
- Project effort (points) is derived automatically from the team's role velocities

### Pipeline Capacity Balancing
- Toggle **proportional capacity balancing** across pipelines
- Each pipeline is ring-fenced a share of monthly capacity equal to its proportion of total project effort
- Prevents high-priority pipelines from starving lower-priority ones
- Unused quota within a month can roll to other pipelines (soft quotas)
- Quota split visualised as a stacked bar in the Gantt view

### Filtering
- A **filter bar** sits above all views with pipeline and role chips
- Click one or more pipeline chips to show only projects from those pipelines
- Click one or more role chips to show only projects involving those roles and team members with those roles
- Filters are multi-select and apply across all three views (Gantt, Team, Summary)
- A **✕ Clear** button resets all active filters; the Summary view shows a banner indicating what's filtered

### Views
| Tab | What it shows |
|-----|--------------|
| **Gantt** | Project bars across the planning horizon, grouped by pipeline, with team member avatars and capacity heatmap |
| **Team** | Per-member monthly utilisation bars, role capacity table, and pipeline distribution table (when balancing is on) |
| **Summary** | Key metrics, team capacity table, pipeline allocation breakdown, and full project schedule table |

### CSV Import
Import projects from a CSV file with the following columns:

```
name, pipeline, points, priority, earliest_start, color, blocks, roles
```

- `roles` format: `Developer:60;Designer:40`
- `blocks` format: semicolon-separated project names that must finish before this one starts
- Download a template from within the app

---

## Getting Started

1. Download `programme-modeller.html`
2. Open it in any modern browser
3. Add team members in the sidebar (name, role, velocity, and optionally a max capacity)
4. Add projects manually or import via CSV
5. Adjust settings (horizon, start month, velocity growth, pipeline balancing)
6. Use the filter bar to focus on specific pipelines or roles
7. Switch between Gantt, Team, and Summary tabs

No installation required.

---

## Settings Reference

| Setting | Description |
|---------|-------------|
| Planning horizon | Number of months to schedule across (3–36) |
| Start month | The calendar month the plan begins |
| Velocity growth rate | Compound % increase in each member's velocity per month (0 = flat) |
| Pipeline capacity balancing | Distributes monthly capacity proportionally across pipelines by effort share |

---

## Team Member Fields

| Field | Description |
|-------|-------------|
| Name | Member's name |
| Role | Role used to match them to projects (e.g. Developer, Designer, QA) |
| Base velocity | Story points per month at the start of the horizon |
| Max capacity | Optional ceiling — growth from the velocity rate will never exceed this value |

---

## Project Fields

| Field | Description |
|-------|-------------|
| Name | Project name |
| Pipeline | Group/stream the project belongs to |
| Priority | Lower number = higher priority (1 = highest) |
| Earliest start | Month offset before which the project cannot begin (0 = can start immediately) |
| Colour | Hex colour for Gantt and charts |
| Role effort split | Which roles are needed and in what proportion |
| Blocks | Other projects that must complete before this one can start |

---

## How Scheduling Works

Projects are sorted by priority, then scheduled month by month:

1. Blockers are checked — a project won't start until all projects it depends on are complete
2. Each project's effort is split across roles (e.g. 60pts Developer, 40pts Designer)
3. Available member capacity is consumed greedily within each role
4. If **pipeline balancing** is on, each pipeline's spend per month is capped at its proportional quota before capacity is allocated to the next pipeline
5. Velocity growth is applied as `base_velocity × (1 + rate/100)^month`, then clamped to the member's max capacity if set
6. Any effort that can't be met within the horizon is reported as overflow

---

## File Structure

Everything lives in a single `programme-modeller.html` file:

```
programme-modeller.html
├── <style>        Design tokens, layout, component styles
├── <body>         Sidebar (settings, team, projects) + main panel (tabs + filter bar)
└── <script>
    ├── State      teamMembers[], projects[], filterPipelines, filterRoles
    ├── Helpers    velocity growth, pipeline fractions, month labels
    ├── Filters    renderFilterBar(), projectMatchesFilter(), memberMatchesFilter()
    ├── Scheduler  scheduleWithDeps() — core planning engine
    └── Renderers  renderGantt(), renderTeam(), renderSummary(), renderSidebar()
```

---

## Contributing

The tool is intentionally self-contained. To make changes, edit `programme-modeller.html` directly — no build process needed. The scheduler logic lives in `scheduleWithDeps()` and the render functions each handle one view.

---

## Licence

MIT
