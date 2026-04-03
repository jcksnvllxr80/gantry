# Plan: Labeled Vertical Markers on the Timeline

## Problem
Gantry has no way to place labeled vertical markers on the timeline — things like "Today", "Launch Goal", "Board Review", etc. The `today-highlight` CSS exists (rendered by frappe-gantt internally) but it's just a faint background shading with no label or user control. There is no general-purpose marker system.

## Proposed Approach
Add a **markers system** that lets users place named, dated, colored vertical lines on the Gantt chart. Markers are persisted in JSON alongside tasks/phases, managed through a sidebar UI section, and rendered as SVG `<line>` + `<text>` elements using the same coordinate math as weekend/holiday shading.

A special **"Today" marker** auto-updates its date to the current day on every render, but can be toggled on/off.

---

## Data Model

```js
// In-memory array (parallel to `tasks`, `phases`, `holidaySet`)
var markers = [];

// Each marker:
{
  id:    "marker-today",        // stable ID
  label: "Today",               // displayed text
  date:  "2026-04-03",          // ISO date (auto-updated for "Today")
  color: "#58a6ff",             // line color (default: accent)
  isToday: true                 // optional flag — auto-pins to current date
}
```

### JSON persistence (version-safe)
Export adds `markers` array to the config object:
```json
{
  "version": 2,
  "title": "...",
  "phases": [...],
  "tasks": [...],
  "markers": [
    { "id": "marker-today", "label": "Today", "date": "2026-04-03", "color": "#58a6ff", "isToday": true },
    { "id": "marker-goal",  "label": "Launch Goal", "date": "2026-06-15", "color": "#2da44e" }
  ]
}
```
Import reads `data.markers` if present (defaults to `[]` for backward compatibility with existing configs).

---

## Rendering

### SVG structure
A new function `drawMarkers(svg)` called right after `drawWeekendShading(svg)` inside the existing `setTimeout` block (~line 1450).

```
<g class="marker-lines">
  <line class="marker-line" x1="X" y1="hdrH" x2="X" y2="svgH" stroke="#58a6ff" />
  <text class="marker-label" x="X+3" y="hdrH+12" fill="#58a6ff">Today</text>
</g>
```

### Coordinate calculation (same pattern as weekend shading)
```js
var dayIndex = daysBetween(toISO(gantt.gantt_start), marker.date);
var x = dayIndex * dayWidthForView(currentView());
```

### Visibility rules
- Markers render in Day and Week views (skip Month — too compressed, same as weekends).
- Lines span from header bottom (`hdrH`) to SVG bottom (`svgH`).
- Labels rendered just below the header, rotated or offset to avoid overlap.
- The "Today" marker auto-updates `marker.date = toISO(new Date())` on every `drawMarkers()` call.

---

## CSS / Theming

```css
/* Default (dark) */
.marker-lines line.marker-line   { stroke-width: 2; stroke-dasharray: 6 3; opacity: 0.85; }
.marker-lines text.marker-label  { font-size: 11px; font-weight: 600; }

/* Light mode */
:root.light .marker-lines line.marker-line  { opacity: 0.7; }
:root.light .marker-lines text.marker-label { /* inherits fill from inline color */ }
```

Markers use inline `stroke`/`fill` set to each marker's `color` so they can be individually colored. The CSS handles shared properties like dash pattern, font size, and opacity.

---

## UI — Sidebar Section

Add a **"Markers"** collapsible section to the sidebar (below Stats, above or below Phase Manager).

### Marker list
Each marker shown as a row:
```
[color swatch] [label input] [date input] [delete btn]
```
- Color swatch → opens `<input type="color">`
- Label → `<input type="text">`
- Date → `<input type="date">` (disabled for "Today" markers — auto-set)
- Delete → `×` button (the "Today" marker can be toggled off but not deleted, or it can be deleted and re-added)

### Add marker button
`+ Add Marker` button at bottom (same pattern as `+ Add Phase`).

### Today toggle
A small dedicated toggle or the marker row itself, to quickly turn the auto-today line on/off. When the user creates a new chart, "Today" is included by default.

---

## Import / Export Changes

### Export (`btnSave` handler, ~line 1807)
Add `markers: markers.map(serialiseMarker)` to the JSON payload.

```js
function serialiseMarker(m) {
  return { id: m.id, label: m.label, date: m.date, color: m.color, isToday: !!m.isToday };
}
```

### Import (`parseConfigPayload` + `applyLoadedConfig`)
- `parseConfigPayload`: extract `data.markers || []`
- `applyLoadedConfig`: set `markers = parsed.markers` and re-render
- Backward compatible: old files without `markers` key just get an empty array

---

## Todos

1. **data-model** — Add `markers` array, default "Today" marker, `serialiseMarker()` / `deserialiseMarker()` helper functions
2. **render-markers** — Create `drawMarkers(svg)` function, wire into render lifecycle after `drawWeekendShading`
3. **css-markers** — Add marker-line and marker-label styles for both dark and light themes
4. **sidebar-ui** — Build marker manager UI section in sidebar (list, add, edit, delete)
5. **json-export** — Add markers to JSON export payload
6. **json-import** — Read markers from imported JSON, backward-compatible default
7. **today-auto** — Auto-update "Today" marker date on each render; default-on for new charts
8. **manual-test** — Verify in browser: add/edit/delete markers, export, reimport, theme switch, view mode switch

---

## Notes & Considerations

- **Label collision**: If two markers are on the same date or adjacent dates, labels may overlap. A simple offset or stagger could help but is not critical for v1.
- **Month view**: Markers are hidden in Month view (same as weekend shading) because the day resolution is too small. Could revisit later.
- **Print/export**: Markers should appear in PNG and PDF exports since they are SVG elements rendered by `html2canvas`/`jspdf` — verify during manual test.
- **No frappe-gantt changes**: This is a pure overlay, no modifications to the library needed.
- **Version field**: Keep `version: 2` — markers are additive and backward-compatible (old readers just ignore the key).
