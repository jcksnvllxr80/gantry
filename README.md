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
6. Use `Save JSON` to export the current state.

## Edit An Existing Chart

1. Open `gantt.html` in a browser.
2. Browse for your previously saved chart config JSON, or load a phase-only JSON first and then add tasks.
3. Browse for (or create) your holidays file (json) to define holidays for shading and workday scheduling.
4. Decide whether to disable weekends for workday-based scheduling.
5. When done editing, save to PNG or PDF, and export the updated JSON.

## Phase Sets

Phases are user-defined. You can:

- use the right sidebar tabs: `Task` for task editing, `Phases` for phase management
- create, rename, recolor, and delete phases in the `Phases` tab
- save the current phase set as a phase-only JSON file
- load a phase-only JSON file before adding tasks
- save full chart JSON files that include both `phases` and `tasks`

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

Rules:

- `phase`: stores the phase `id` from the loaded phase set
- `dependencies`: comma-separated task IDs
- `daysOfEffort` counts the start date as day 1
- dates use `YYYY-MM-DD`
- `done: true` shows complete styling
- `tbd: true` shows estimate-TBD styling

Older data with `start` and `end` still loads, but the app saves tasks back as `daysOfEffort`.

## Features

- Single-file app with no build step
- User-defined phases with in-app create/load/save
- Day, week, and month timeline views
- Row height and timeline width zoom controls
- Drag-to-reschedule editing
- Downstream dependency cascading on date moves
- Effort resizing without automatic date cascading
- Link mode for creating dependency arrows
- Dependency editing in the sidebar
- Multi-select, batch edit, and copy/paste
- Done and TBD task states
- Weekend exclusion for workday-based scheduling
- Holiday loading and holiday shading
- Light and dark theme toggle
- JSON import and export
- PNG and PDF export
- Reset back to the loaded config

## Notes

- Theme is saved in browser `localStorage`
- Third-party library notices are listed in `THIRD_PARTY_NOTICES.md`
