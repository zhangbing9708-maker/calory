# calory

**Macro Ledger** – a single-file calorie and macro tracker in the spirit of MacroFactor.

Everything lives in [`index.html`](index.html): no build step, no dependencies beyond two Google Fonts.

## What it does

- **Targets** – Mifflin-St Jeor resting energy × activity multiplier, a goal (lose / maintain / gain) at a rate expressed as % of body weight per week, then a protein / fat / carb split. Every number can be edited before saving.
- **Today** – a hero ring with calories left, three macro meters, and the day's log grouped by meal. Log food by **describing it**, by **photo**, or **by hand**; log **exercise** by description, quick chips, or a watch reading. Burned calories can be added back to the day's budget (switch on the Targets tab).
- **Trends** – 14-day calories vs target, a smoothed weight trend, and a weekly check-in that estimates real expenditure from intake and weight change, then suggests an updated target.

## Where the AI comes from

The food, photo and exercise estimates use the Claude artifact runtime (`window.claude.use("sample")`). They are only available when the page is published as a Claude artifact, where the log is also stored in the viewer's private Claude account storage (`use("db")`).

Opened anywhere else (GitHub Pages, a local file), the page still works as a tracker: targets, by-hand logging, exercise by hand, weight, and trends all run. The Describe and Photo buttons are disabled because there is no model to ask.

## Keeping the log safe on the website: Google sign-in

On the website the log is saved in the browser's `localStorage` and, once you sign in with Google, mirrored to a private file (`macro-ledger.json`) in the **app data folder** of your Google Drive. That folder is hidden from the normal Drive view, only this app can read it, and it travels with your Google account, so you can open the site on another device, sign in, and your log is there. Days are merged by their last-edit time, so two devices can both log without clobbering each other.

The browser copy stays the working copy: logging works offline, and changes sync the next time Drive is reachable. Nothing is sent to any other server.

### One-time setup (about five minutes)

1. Turn on GitHub Pages for this repo: **Settings → Pages → Build and deployment: Deploy from a branch → `main` / root**. Your site will be at `https://<your-user>.github.io/calory/`.
2. In [Google Cloud Console](https://console.cloud.google.com/) create a project (any name), then **APIs & Services → Library → Google Drive API → Enable**.
3. **APIs & Services → OAuth consent screen**: External, give it a name and your email, add the scopes `.../auth/drive.appdata` and `.../auth/userinfo.email` (both are non-sensitive, no verification needed). While the app is in *Testing*, add your own Google address under **Test users**, or press **Publish app** so any account can sign in.
4. **APIs & Services → Credentials → Create credentials → OAuth client ID → Web application**. Under **Authorized JavaScript origins** add `https://<your-user>.github.io` (no path). Copy the client ID, which ends in `.apps.googleusercontent.com`.
5. Paste it into [`config.js`](config.js):

   ```js
   window.MACRO_LEDGER_CONFIG = { googleClientId: "1234567890-abc.apps.googleusercontent.com" };
   ```

Reload the site: the card at the bottom of the Today tab now offers **Sign in with Google**. Google sign-in only works over `https`, so it will not work from a `file://` copy.

## Data model

One document per day, one profile document, stored under the viewer's private `data/users/<id>/` subtree (or `localStorage` keys prefixed `ml:` outside Claude):

```json
{ "type": "day", "date": "2026-10-20", "weight": 78.4,
  "entries":  [{ "id": "…", "name": "Scrambled eggs", "portion": "2 large eggs", "kcal": 180, "p": 12, "c": 2, "f": 14, "meal": "Breakfast", "time": "07:40", "source": "text" }],
  "exercise": [{ "id": "…", "name": "Easy run", "minutes": 45, "kcal": 420, "intensity": "moderate", "time": "17:10", "source": "text" }] }
```
