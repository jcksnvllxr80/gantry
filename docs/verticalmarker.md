# Vertical Marker Feature — Exploration Results

## Summary: Marker and Vertical Line Features in gantt.html

### FINDINGS:

#### 1. "Today" Feature - PARTIALLY EXISTS (CSS only, NOT rendered)
- **CSS Classes Defined (Lines 167, 183, 214):**
  - `.today-highlight` with fill styling exists in 3 places:
    - **Dark mode** (line 167): `fill: rgba(88,166,255,.1)` with opacity 1
    - **Light mode** (line 183): `fill: #fcf8e3` with opacity 0.5
    - **Default** (line 214): `fill: rgba(9,105,218,.06)`

- **JavaScript Implementation (Lines 846-861):**
  - Function `restoreTimelineViewport()` calculates today's position:
    ```javascript
    var today = toISO(new Date());
    var x = daysBetween(toISO(gantt.gantt_start), today) * dayWidthForView(view);
    var target = Math.max(0, x - host.clientWidth / 3);
    host.scrollLeft = target;
    ```
  - **This only scrolls the timeline to show "today" — it does NOT draw a visual marker/line**

- **Status:** The CSS class exists but **no SVG rect or line is ever created with the `today-highlight` class**. The feature is prepared but not implemented.

#### 2. Vertical Lines/Markers - DOES NOT EXIST
- No "vertical-line", "vert-line", or "todayLine" markers found
- No code draws vertical SVG lines for today or any other markers
- No "goal" or "milestone" markers exist

#### 3. Annotations on Timeline - DOES NOT EXIST
- No timeline annotations functionality found
- No "annotation" class or functions in the code

#### 4. SVG Line Elements - ONLY Weekend/Holiday Shading Exists
- **Weekend/Holiday Shading (Lines 1164-1210):**
  - Function `drawWeekendShading(svg)` creates vertical **rectangles** (not lines) for weekends and holidays
  - Creates SVG `<rect>` elements with classes:
    - `weekend-cell`
    - `holiday-cell`
    - `weekend-holiday-cell`
  - These are positioned vertically with the same height as the gantt chart
  - No vertical SVG `<line>` elements are used

#### 5. Configuration Files
- **conf/funny-holidays-demo.json** — Only contains holiday definitions (no marker config)
- **conf/demo-config-funny.json** — Task data only (no marker config)

### Key Code Snippets:

**CSS Classes (defined but not rendered):**
```css
.today-highlight { fill: rgba(9,105,218,.06) !important; }
```

**JavaScript that calculates today but doesn't draw it (Lines 846-861):**
```javascript
function restoreTimelineViewport() {
  var host = ganttScrollHost();
  if(!host || !gantt) return;
  if(timelineScrollLeft != null) {
    host.scrollLeft = timelineScrollLeft;
    return;
  }
  if(!shouldApplyDefaultViewport) return;
  var view = currentView();
  if(view !== 'Week' && view !== 'Day') return;
  var today = toISO(new Date());
  var x = daysBetween(toISO(gantt.gantt_start), today) * dayWidthForView(view);
  var target = Math.max(0, x - host.clientWidth / 3);
  host.scrollLeft = target;
  shouldApplyDefaultViewport = false;
}
```

**Weekend/Holiday Rectangle Creation (Lines 1192-1199):**
```javascript
var rect = document.createElementNS('http://www.w3.org/2000/svg','rect');
rect.setAttribute('x', String(x));
rect.setAttribute('y', String(hdrH));
rect.setAttribute('width', String(dayWidth));
rect.setAttribute('height', String(svgH - hdrH));
rect.setAttribute('class', isWeekend && isHoliday ? 'weekend-holiday-cell' : (isHoliday ? 'holiday-cell' : 'weekend-cell'));
```

### Conclusion
- ✅ **Weekend/Holiday shading** — FULLY IMPLEMENTED (vertical rectangles)
- ⚠️ **"Today" marker** — PARTIALLY IMPLEMENTED (scroll logic exists, but visual element not rendered)
- ❌ **Vertical "today" line** — NOT IMPLEMENTED
- ❌ **Goal/Milestone markers** — NOT IMPLEMENTED
- ❌ **Generic timeline annotations** — NOT IMPLEMENTED
