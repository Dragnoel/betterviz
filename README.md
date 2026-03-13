# betterviz

Interactive multi-dimensional data visualizer. Load CSV data, map dimensions to axes, and explore in a grid of scatter plots.

## Quick start

0. For local, offline option, download tools/local-visualizer.html to your computer. Open using a browser, load your CSV and media files (keep in the same folder for convenience). Interact.

For online or server type:
1. **Get the repo** — `git clone https://github.com/Dragnoel/betterviz` (or [download](https://github.com/Dragnoel/betterviz))
2. **Install Hugo Extended** — Windows: `winget install Hugo.Hugo.Extended`; macOS: `brew install hugo` (or [download](https://github.com/gohugoio/hugo/releases))
3. **Open a terminal** in the project folder and run:
   ```
   hugo server
   ```
4. **Open in browser:** http://localhost:1313/

**Useful links:**
- **Full editor** (upload your own CSV): http://localhost:1313/
- **Examples:** http://localhost:1313/examples/
- **Kourosh demo:** http://localhost:1313/examples/kourosh/

**Note:** For more detailed setup instructions, see the Step-by-step guide below.

---

## How the data is interpreted

This app is a multi-dimensional data visualizer. Each CSV row is a record; columns are fields the app can use as dimensions, media, or descriptive info.

It assumes **four column types**:
- **Dimensions (`dim::`)** — categorical values used for axes/color/shape. Example: `dim::Function`, `dim::Intonation`, `dim::Focus`.
- **Media (`med::`)** — URLs for audio/image/video. Example: `med::audio_url`, `med::image_url`, `med::video_url`.
- **Descriptive text (`desc::`)** — extra info shown in the record modal. Example: `desc::translation`, `desc::gloss`, `desc::transcription`, `desc::id`.
- **Reserved/Metadata (`res::`)** — metadata like titles/names (used for modal header, not as dimensions). Example: `res::title`, `res::name`, `res::description`.

### Two ways to provide types

**Version A — Auto-detect (works, but less reliable):**
If you upload a CSV with plain headers, the app infers dimensions by excluding:
- media-like columns (`audio_url`, `image_url`, `video_url`, etc.)
- descriptive columns (`gloss`, `transcription`, `translation`, `description`, etc.)
- reserved/meta columns (`title`, `name`)
- ID-like columns (first `id` column or unique-per-row columns)

**Version B — Explicit prefixes (preferred):**
Prefix column names with `dim::`, `med::`, `desc::`, or `res::` to explicitly set the type. The app will strip the prefix in the UI, but the prefix controls how the column is treated.

### Intuitive example (recipes)
Imagine you have a dataset of cooking recipes with columns like recipe name, cuisine, difficulty level, dietary category, description, and a photo. Fields such as cuisine, difficulty level, and dietary category make good dimensions because you might want to compare or explore recipes across them. For example, you may want to see how many easy vs. difficult recipes exist for different cuisines, or comparing vegetarian and non-vegetarian dishes.

Fields like recipe name are still important for identifying each recipe, but you usually do not want to treat them as axes in a visualization, so they should be treated as reserved metadata instead. Fields like description provide additional context about the recipe and are best treated as descriptive text, shown when inspecting a record rather than used for grouping or comparison.

Meanwhile, the photo is media, which can be displayed when viewing a recipe but is not useful as a visualization dimension. The table below shows how to label columns so the tool interprets each field correctly.

| desc::id | res::title           | dim::cuisine   | dim::difficulty | dim::dietary_category | desc::description                                     | med::image_url                                                                 |
| -------- | -------------------- | -------------- | --------------- | --------------------- | ----------------------------------------------------- | ------------------------------------------------------------------------------ |
| r001     | Spaghetti Carbonara  | Italian        | Medium          | Non-vegetarian        | Classic Roman pasta with eggs, pecorino, and pancetta | [https://example.org/img/carbonara.jpg](https://example.org/img/carbonara.jpg) |
| r002     | Vegetable Stir Fry   | Chinese        | Easy            | Vegan                 | Quick wok-fried vegetables with soy and garlic        | [https://example.org/img/stirfry.jpg](https://example.org/img/stirfry.jpg)     |
| r003     | Chicken Tikka Masala | Indian         | Medium          | Non-vegetarian        | Spiced grilled chicken in creamy tomato sauce         | [https://example.org/img/tikka.jpg](https://example.org/img/tikka.jpg)         |
| r004     | Mushroom Risotto     | Italian        | Medium          | Vegetarian            | Creamy arborio rice with sautéed mushrooms            | [https://example.org/img/risotto.jpg](https://example.org/img/risotto.jpg)     |
| r005     | Fish Tacos           | Mexican        | Easy            | Pescatarian           | Grilled fish tacos with cabbage and lime crema        | [https://example.org/img/tacos.jpg](https://example.org/img/tacos.jpg)         |
| r006     | Falafel Wrap         | Middle Eastern | Easy            | Vegan                 | Crispy falafel balls wrapped with tahini sauce        | [https://example.org/img/falafel.jpg](https://example.org/img/falafel.jpg)     |
| r007     | Pad Thai             | Thai           | Medium          | Non-vegetarian        | Sweet and tangy noodles with peanuts and shrimp       | [https://example.org/img/padthai.jpg](https://example.org/img/padthai.jpg)     |
| r008     | Shakshuka            | Middle Eastern | Easy            | Vegetarian            | Eggs poached in spicy tomato and pepper sauce         | [https://example.org/img/shakshuka.jpg](https://example.org/img/shakshuka.jpg) |




---

## Step-by-step guide (first-time setup)

If you’ve never used a terminal or installed software like this before, follow these steps.

### 1. Get the repo

You can either clone it with git or download a zip.

**Option A — Git clone:**
```
git clone https://github.com/Dragnoel/betterviz
```

**Option B — Download zip:**  
Go to https://github.com/Dragnoel/betterviz and download the repo as a zip, then unzip it.

### 2. Install Hugo

Hugo is a small program that builds and runs the visualizer. You only need to install it once.

**Option A — Windows (recommended):**
1. Press the **Windows key**, type **PowerShell**, and open it.
2. Copy and paste this, then press **Enter**:
   ```
   winget install Hugo.Hugo.Extended --accept-package-agreements --accept-source-agreements
   ```
3. When it finishes, **close PowerShell and open a new one** so your computer recognizes Hugo.

**Option B — macOS (recommended):**
1. Open **Terminal**.
2. Copy and paste this, then press **Enter**:
   ```
   brew install hugo
   ```

**Option C — Manual download:**  
Go to [Hugo releases](https://github.com/gohugoio/hugo/releases), download the **extended** build for your OS, unzip it, and put the `hugo` binary in a folder that’s in your PATH.

### 3. Open a terminal and go to the project folder

1. Open **PowerShell** (Windows) or **Terminal** (macOS).
2. Type this (change the path if your project is elsewhere) and press **Enter**:
   ```
   cd /path/to/betterviz
   ```
3. The prompt should show that folder. If you see “cannot find path”, check that the path is correct.

### 4. Start the visualizer

1. Type this and press **Enter**:
   ```
   hugo server
   ```
2. You should see: **Web Server is available at http://localhost:1313/**
3. **Leave this window open** while you use the visualizer.

### 5. Open it in your browser

1. Open Chrome, Edge, or Firefox.
2. In the address bar, type: **http://localhost:1313/** and press **Enter**.

**To stop:** Go back to the PowerShell window and press **Ctrl+C**.

---

## Troubleshooting

| Problem | What to try |
|--------|-------------|
| "hugo is not recognized" | Install Hugo and open a **new** PowerShell window. |
| "cannot find path" | Check that the path in the `cd` command matches your project folder. |
| Page won't load | Make sure the PowerShell window with `hugo server` is still open. |
| Port 1313 in use | Run `hugo server --port 1314` and use http://localhost:1314/ instead. |
