# Gantry

Single-file browser Gantt chart creator for delivery planning.

## Files

- `gantt.html` - app UI and all logic

## Create The Chart

1. Open `gantt.html` in a browser.
2. Click `Add Task`.
3. Enter task name, start date, days of effort, assignee, phase, and note.
4. Use `Link` to connect dependencies.
5. Use `Save JSON` to export the current state.

## Update an existing Chart

1. Open `gantt.html` in a browser.
2. Browse for your previously saved config file (json) to define the default tasks.
3. Browse for (or create) your holidays file (json) to define holidays for shading and workday scheduling.
4. Decide whether to disable weekends for workday-based scheduling.
5. When done editing, save to PNG or PDF, and export the updated JSON.

## Task Format

```json
{
  "id": "service1",
  "name": "Service 1",
  "start": "2026-04-06",
  "daysOfEffort": 15,
  "progress": 0,
  "dependencies": "service 0",
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

- Day, Week, and Month timeline views
- Row height and timeline width zoom controls
- Drag to reschedule tasks
- Downstream dependency cascade on date moves
- Resize task effort without cascading dates
- Link mode for creating dependency arrows
- Dependency add/remove in the sidebar
- Multi-select and batch edit
- Copy/paste selected tasks
- Done and TBD task states
- Weekend exclusion for workday-based scheduling
- Holiday file loading and holiday shading
- Light and dark theme toggle
- Save JSON and load JSON
- PNG and PDF export
- Reset back to your config

## Notes

- Theme is saved in browser `localStorage`
