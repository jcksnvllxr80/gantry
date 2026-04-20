# Gantry

A single-file, browser-based Gantt chart tool for delivery planning.

Gantry lets you build and edit project timelines locally in the browser, manage
dependencies, account for holidays and workdays, and export the result as JSON,
PNG, or PDF.

## Files

- `gantt.html` - app UI and all logic

## Getting Started

1. Open `gantt.html` in a browser.
2. Create phases in the sidebar, or load `conf/phases-starter.json`.
3. Click `Add Task`.
4. Enter task name, start date, days of effort, assignee, phase, and note.
5. Use `Link` to connect dependencies.
6. Use `Save JSON` or `CSV` to export task dates and names.

## Edit An Existing Chart

1. Open `gantt.html` in a browser.
2. Load a previously saved chart config JSON to restore tasks, phases, markers, holidays, and view settings.
3. Optionally load a phase-only JSON first if you want to start a new chart from a saved phase set.
4. Browse for (or create) a holidays JSON file if you want to replace the holidays stored in the project config.
5. Decide whether to disable weekends for workday-based scheduling.
6. When done editing, save to PNG or PDF, and export the updated JSON.

## Phase Sets

Phases are user-defined. You can:

- use the right sidebar tabs: `Task` for task editing, `Global` for phases, legend, markers, and holidays
- create, rename, recolor, and delete phases in the `Global` tab
- save the current phase set as a phase-only JSON file
- load a phase-only JSON file before adding tasks
- save full chart JSON files that include `tasks`, `phases`, `markers`, `holidays`, and `settings`

Clicking a task bar in the chart switches the sidebar back to the `Task` tab automatically.

## Task Format

```json
{
  "id": "task1",
  "name": "Task 1",
  "start": "2026-04-06",
  "daysOfEffort": 15,
  "progress": 0,
  "dependencies": "task0",
  "assignee": "John Doe",
  "phase": "production",
  "done": false,
  "tbd": false,
  "note": ""
}
```

Example phase set:

```json
{
  "phases": [
    { "id": "discovery", "name": "Discovery", "color": "#B35C00" },
    { "id": "production", "name": "Production", "color": "#0969DA" }
  ]
}
```

Example project-level holidays in saved JSON:

```json
{
  "holidayName": "Funny Demo Holidays 2026",
  "holidays": [
    { "date": "2026-04-10", "name": "International Overcaffeinated Pigeon Awareness Day" },
    { "date": "2026-04-14", "name": "Mandatory Tiny Hat Friday" }
  ]
}
```

## Saved Settings

JSON exports include a `settings` object that restores the chart view on load:

```json
{
  "settings": {
    "viewMode": "Week",
    "colWidth": 30,
    "barHeight": 22,
    "theme": "dark"
  }
}
```

- `viewMode`: `"Day"`, `"Week"`, or `"Month"`
- `colWidth`: horizontal zoom level (18–120 px)
- `barHeight`: vertical row height (12–60 px)
- `theme`: `"light"` or `"dark"`

Old JSON files without `settings` load normally using defaults.

Rules:

- `phase`: stores the phase `id` from the loaded phase set
- `dependencies`: comma-separated task IDs
- `daysOfEffort` counts the start date as day 1
- dates use `YYYY-MM-DD`
- `progress`: 0–100, percentage complete
- `done: true` shows complete styling (also sets progress to 100)
- `tbd: true` shows estimate-TBD styling
- `holidays`: project JSON can embed holiday dates and names directly

Older data with `start` and `end` still loads, but the app saves tasks back as `daysOfEffort`.

## Features

- Single-file app with no build step
- File and Edit menus for save/load/export and undo/redo/copy/paste
- Undo/redo with Ctrl+Z / Ctrl+Y (50-level history)
- User-defined phases with in-app create/load/save
- Day, week, and month timeline views
- Row height and timeline width zoom controls
- Drag-to-reschedule editing
- Downstream dependency cascading on date moves
- Effort resizing without automatic date cascading
- Link mode for creating dependency arrows
- Dependency editing in the sidebar
- Multi-select, batch edit, and copy/paste
- Task progress tracking via sidebar slider (0–100%, step 5) or bar drag handle
- Done and TBD task states
- Weekend exclusion for workday-based scheduling
- Holiday loading, holiday list in the `Global` tab, chart shading, and embedded holiday save/load
- Timeline markers (labeled vertical lines for "Today", goals, milestones)
- Light and dark theme toggle
- JSON import and export
- CSV export of task name, start, and end dates
- PNG and PDF export
- Reset back to the loaded config

## Timeline Markers

Markers are labeled vertical lines drawn on the chart — useful for deadlines,
milestones, or tracking today's date.

- Manage markers in the **Global** sidebar tab under **Timeline Markers**
- Each marker has a label, date, and color
- A **Today** marker is included by default and auto-updates its date
- Click **+ Add Marker** to create a new marker (defaults to one week out)
- Markers are saved and restored with JSON exports

Marker format in saved JSON:

```json
{
  "markers": [
    { "id": "marker-today", "label": "Today", "date": "2026-04-03", "color": "#58a6ff", "isToday": true },
    { "id": "marker-goal",  "label": "Launch", "date": "2026-06-15", "color": "#2da44e" }
  ]
}
```

## Holidays

- Manage holidays in the **Global** sidebar tab under **Holidays**
- Clicking a holiday in the list temporarily highlights its chart column
- Hovering a holiday-shaded chart column shows the holiday name and date
- Holidays loaded from a holiday file are saved back into the main project JSON

## Notes

- Theme is saved in browser `localStorage`
- Third-party library notices are listed in `THIRD_PARTY_NOTICES.md`
