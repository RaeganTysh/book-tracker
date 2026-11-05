# Book Tracker

A small iPhone web app that reads and writes `BookTracker.xlsx` in your OneDrive and shows reading stats.

Files in this repo:

| File | What it is |
|---|---|
| `index.html` | The whole app |
| `manifest.webmanifest`, `icon-192.png`, `icon-512.png` | Home-screen name and icon |

---

## 1. Put the spreadsheet in OneDrive

Upload `BookTracker.xlsx` to the **top level** of your OneDrive (not inside a folder).
If you put it in a folder, change `FILE_PATH` in `index.html`, for example `"Documents/BookTracker.xlsx"`.

## 2. Create the GitHub repo and turn on Pages

1. On github.com, click **New repository**. Name it `book-tracker`, set it to **Public**, and create it.
   (GitHub Pages is free for public repos. Your book data is NOT in the repo; it stays in OneDrive.)
2. Click **Add file → Upload files**, drag in `index.html`, `manifest.webmanifest`, `icon-192.png`, `icon-512.png` and this README, then **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment*, set Source = **Deploy from a branch**, Branch = **main**, folder = **/ (root)**, and **Save**.
4. After a minute the page shows your site address:
   `https://YOUR-GITHUB-USERNAME.github.io/book-tracker/`
   Copy it exactly, including the trailing `/`. You need it in the next step.

## 3. Register the app with Microsoft (free, one time)

This is what lets the app sign in and access your OneDrive file.

1. Go to <https://entra.microsoft.com> and sign in with the **same Microsoft account your OneDrive uses**.
   (If it says you have no directory, use <https://portal.azure.com> instead and search for **App registrations**. A free Azure account may be required.)
2. Open **Applications → App registrations → New registration**.
   - **Name:** Book Tracker
   - **Supported account types:** *Accounts in any organizational directory and personal Microsoft accounts*
   - **Redirect URI:** platform **Single-page application (SPA)**, value = your Pages address from step 2.4
   - Click **Register**.
3. On the overview page, copy the **Application (client) ID**.
4. Go to **API permissions → Add a permission → Microsoft Graph → Delegated permissions**, tick **Files.ReadWrite** (User.Read is already there), and click **Add permissions**.

No client secret is needed. Single-page apps don't use one.

## 4. Paste the client ID into the app

In your GitHub repo, open `index.html` and click the pencil icon to edit. Near the top of the `<script>` section:

```js
const CLIENT_ID = "PASTE-YOUR-APPLICATION-CLIENT-ID-HERE";
const FILE_PATH = "BookTracker.xlsx";
```

Paste your ID between the quotes, fix `FILE_PATH` if needed, and **Commit changes**. Pages redeploys within about a minute.

## 5. Add it to your iPhone

1. Open your Pages address in **Safari**.
2. Tap **Sign in with Microsoft** and approve the permission prompt (first time only).
3. Tap the **Share** button → **Add to Home Screen** → **Add**.

It now opens full-screen like an app.

---

## Using it

- **Stats:** totals, books and hours per month (tap a bar to see the titles), fiction vs non-fiction, ratings, source, genres, repeat authors, and 5-star books. Filter by year at the top.
- **Library:** search, sort, and tap a book to see its description and your comment. **Edit** changes or deletes it.
- **Add:** fill in the form and save. It writes straight to the Excel file.
- **Sync:** reloads the file if you've edited it in Excel.

## Good to know

- **Close the file in Excel before saving from the app.** If it's open, OneDrive may lock it and the app will say so.
- If the file changed somewhere else since the app loaded it, the app won't overwrite it. It reloads instead, and you redo the edit.
- The app rewrites the `Books` sheet when it saves. Your data and the `Subscriptions` tab are kept, but Excel table styling and colours on `Books` are reset to plain formatting. Edit in either place; just keep the same column headers.
- **Genre stats** use the first genre in the list (the part before the first `;`).
- **Spotify hrs this month** adds up hours for books from Spotify finished this month, against the 15-hour allowance.

## Troubleshooting

| Message | Fix |
|---|---|
| `AADSTS50011` redirect URI mismatch | The Redirect URI in the app registration must exactly match the Pages address, including the trailing `/`. |
| Couldn't find "BookTracker.xlsx" | The file isn't at the path in `FILE_PATH`. |
| Paste your client ID | `CLIENT_ID` in `index.html` still has the placeholder. |
| Sign-in loops on iPhone | Settings → Safari → turn off **Prevent Cross-Site Tracking** temporarily, sign in once, then turn it back on. |
