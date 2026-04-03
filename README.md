# Gantry

A single-file, browser-based Gantt chart tool for delivery planning.

Gantry lets you build and edit project timelines locally in the browser, manage
dependencies, account for holidays and workdays, and export the result as JSON,
PNG, or PDF.

## Files

- `gantt.html` - app UI and all logic

## Getting Started

1. Open `gantt.html` in a browser.
2. Click `Add Task`.
3. Enter task name, start date, days of effort, assignee, phase, and note.
4. Use `Link` to connect dependencies.
5. Use `Save JSON` to export the current state.

## Edit An Existing Chart

1. Open `gantt.html` in a browser.
2. Browse for your previously saved config file (json) to define the default tasks.
3. Browse for (or create) your holidays file (json) to define holidays for shading and workday scheduling.
4. Decide whether to disable weekends for workday-based scheduling.
5. When done editing, save to PNG or PDF, and export the updated JSON.

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
  "phase": 1,
  "done": false,
  "tbd": false,
  "note": ""
}
```

Rules:

- `phase`: `1` = POC, `2` = Production
- `dependencies`: comma-separated task IDs
- `daysOfEffort` counts the start date as day 1
- dates use `YYYY-MM-DD`
- `done: true` shows complete styling
- `tbd: true` shows estimate-TBD styling

Older data with `start` and `end` still loads, but the app saves tasks back as `daysOfEffort`.

## Features

- Single-file app with no build step
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
