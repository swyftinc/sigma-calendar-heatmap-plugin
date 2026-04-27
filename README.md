# Sigma Calendar Heatmap Plugin

A calendar heatmap plugin for [Sigma Computing](https://sigmacomputing.com) that displays daily aggregated metrics with intensity-based color coding — the darker the cell, the higher the value.

**Live URL:** `https://swyftinc.github.io/sigma-calendar-heatmap-plugin/`

**Upstream:** Forked from [twells89/sigma-calendar-heatmap-plugin](https://github.com/twells89/sigma-calendar-heatmap-plugin) — original author: TJ Wells (Sigma).

---

## What It Does

This plugin takes a dataset with a date column and a numeric value column, aggregates the values by calendar day, and renders them in a month grid where each cell's background color scales with the value. High-traffic days become dark; low or zero days stay light.

This is ideal for visualizing:
- Items due per day
- Ticket/case volume by day
- Revenue or orders per day
- Any metric where daily patterns and spikes matter

---

## Features

- **Heatmap coloring** — cell intensity scales linearly from the minimum to the maximum value in the current month
- **7 color themes** — Swyft (default), Red, Blue, Green, Purple, Orange, Teal
- **Light / Dark / Auto theme** — transparent background that adapts to the workbook's color scheme
- **Raleway + Roboto typography** — Raleway for text, Roboto for numerics
- **Monthly KPI header** — shows the aggregated total for the displayed month with a configurable label
- **6 aggregation methods** — Sum, Count, Count Distinct, Average, Max, Min
- **In-plugin filter** — searchable text filter on any column, applied before aggregation
- **Prev/Next month navigation**
- **Click a day** — sets a Sigma workbook variable and fires an action trigger; selected day stays highlighted
- **Configurable title, subtitle, and KPI label**
- **First day of week** — Sunday or Monday

---

## Registering the Plugin in Sigma

1. Go to **Administration → Plugins**
2. Click **Add Plugin**
3. Fill in:
   - **Name:** Calendar Heatmap
   - **URL:** `https://twells89.github.io/sigma-calendar-heatmap-plugin/`
4. Save

---

## Adding to a Workbook

1. In a workbook in **Edit** mode, click **+** → **Plugins** → **Calendar Heatmap**
2. Resize the element — the calendar fills 100% of it
3. Open the editor panel to configure

---

## Configuration Reference

### Required

| Field | Description |
|---|---|
| **Data Source** | The Sigma table or visualization containing your data |
| **Date Column** | A date or datetime column to group events by day |
| **Value Column** | A numeric column to aggregate per day |
| **Tooltip Detail Column** | *(optional)* String column shown as a row list inside the hover tooltip |
| **Filter Column** | *(optional)* String column the in-plugin search box filters against. Leave unset to hide the filter input. |

### Data Options

| Field | Default | Description |
|---|---|---|
| **Aggregation Method** | Sum | How to combine multiple rows on the same day: Sum, Count, Average, Max, or Min |

### Display Options

| Field | Default | Description |
|---|---|---|
| **Title** | Calendar | Displayed at the top of the plugin |
| **Subtitle** | *(blank)* | Smaller text below the title (e.g. "by Day") |
| **KPI Label** | *(same as title)* | Label next to the monthly total number |
| **Show Monthly Total** | On | Shows the large aggregate KPI number for the current month |
| **Color Theme** | Swyft | Heatmap color scale. Options: Swyft (`#261FF6`), Red, Blue, Green, Purple, Orange, Teal |
| **Theme Mode** | Auto | Light, Dark, or Auto (follows the workbook viewer's OS color scheme). Element background is transparent so the workbook background shows through. |

### Calendar Options

| Field | Default | Description |
|---|---|---|
| **First Day of Week** | Sunday | Sunday or Monday |

### Interactivity

| Field | Type | Description |
|---|---|---|
| **Selected Date Variable** | Variable | Set to the clicked day's ISO date string (e.g. `2025-04-15`) when a day is clicked |
| **On Day Click** | Action Trigger | Fires when any in-month day cell is clicked. Wire this to filter other workbook elements. |

---

## Drill-through Pattern (clicking through to underlying data)

Sigma plugins don't have native drill-through — but the heatmap exposes the click event to Sigma's Action system, which gives you the same UX. End-to-end setup:

1. **Create a workbook control variable** (e.g. text control named `clicked_date`).
2. **Wire the plugin** in the editor panel:
   - **Selected Date Variable** → `clicked_date`
   - **On Day Click** → leave empty for now; you'll attach an Action next.
3. **Build the drill target** — a table or modal-page containing the detail rows you want to surface, filtered where `date_column = [clicked_date]`.
4. **Add an Action** to the plugin element:
   - **When:** On click
   - **Do:** Open a modal (or navigate, or filter) → point at the drill target you just built.
   - Save. Sigma writes the action ID into the plugin's `On Day Click` field automatically.
5. **Click any day** — the variable updates, the action fires, the detail view opens. The clicked cell stays outlined so users keep their place.

---

## Data Format

Your data can have multiple rows per day (the plugin aggregates them) or one row per day (pre-aggregated). Dates are accepted in any of these formats:

| Format | Example |
|---|---|
| ISO date | `2025-04-15` |
| ISO datetime | `2025-04-15T10:30:00Z` |
| Unix seconds | `1744675200` |
| Unix milliseconds | `1744675200000` |

---

## Color Intensity

The heatmap scales from the lowest color to the highest based on the **maximum value within the current month**. Navigating to a different month recalculates the scale — so relative intensity is always within-month.

Days with no data show a neutral light background with no value label.

---

## Local Development

```bash
git clone https://github.com/swyftinc/sigma-calendar-heatmap-plugin.git
cd sigma-calendar-heatmap-plugin
npm install
npm run dev
# → http://localhost:3002/sigma-calendar-heatmap-plugin/
```

To test in Sigma: add a Plugins element → **Sigma Plugin Dev Playground** → element menu → **Point to Development URL** → `http://localhost:3002/sigma-calendar-heatmap-plugin/`

---

## Tech Stack

- [React 18](https://react.dev/) — custom calendar grid (no FullCalendar dependency)
- [@sigmacomputing/plugin](https://www.npmjs.com/package/@sigmacomputing/plugin)
- [Vite 5](https://vitejs.dev/)
