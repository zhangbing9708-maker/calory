# calory

**Macro Ledger** – a single-file calorie and macro tracker in the spirit of MacroFactor.

Everything lives in [`index.html`](index.html): no build step, no dependencies beyond two Google Fonts.

## What it does

- **Targets** – Mifflin-St Jeor resting energy × activity multiplier, a goal (lose / maintain / gain) at a rate expressed as % of body weight per week, then a protein / fat / carb split. Every number can be edited before saving.
- **Today** – a hero ring with calories left, three macro meters, and the day's log grouped by meal. Log food by **describing it**, by **photo**, or **by hand**; log **exercise** by description, quick chips, or a watch reading. Burned calories can be added back to the day's budget (switch on the Targets tab).
- **Trends** – 14-day calories vs target, a smoothed weight trend, and a weekly check-in that estimates real expenditure from intake and weight change, then suggests an updated target.

## Where the AI and sync come from

The food, photo and exercise estimates, and the private cross-device sync, use the Claude artifact runtime (`window.claude.use("sample")`, `use("db")`, `use("user")`). They are only available when the page is published as a Claude artifact.

Opened anywhere else (GitHub Pages, a local file), the page still works as a tracker: targets, by-hand logging, exercise by hand, weight, and trends all run, and the log is kept in the browser's `localStorage`. The Describe and Photo buttons are disabled because there is no model to ask.

## Data model

One document per day, one profile document, stored under the viewer's private `data/users/<id>/` subtree (or `localStorage` keys prefixed `ml:` outside Claude):

```json
{ "type": "day", "date": "2026-10-20", "weight": 78.4,
  "entries":  [{ "id": "…", "name": "Scrambled eggs", "portion": "2 large eggs", "kcal": 180, "p": 12, "c": 2, "f": 14, "meal": "Breakfast", "time": "07:40", "source": "text" }],
  "exercise": [{ "id": "…", "name": "Easy run", "minutes": 45, "kcal": 420, "intensity": "moderate", "time": "17:10", "source": "text" }] }
```
