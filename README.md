# KidiVerse – AI-Enhanced Hugo Note Theme

KidiVerse is a **Hugo static-site theme for Obsidian-style Markdown notebooks**.
It respects the typical Obsidian convention where **images live in an `attachments/` sub-folder** next to each note, and automatically resolves those links.
It offers a clean two-column dark layout that mirrors your folder structure and scales gracefully from small personal wikis to large knowledge gardens.

---
![image](https://github.com/user-attachments/assets/d963cf72-7331-4439-b9e1-12f58ac1ff73)


## ✨ Features

* Two-column dark UI – collapsible sidebar auto-reads your `content/` tree
* **Obsidian-style images** – write `![alt](image.png)` and make sure the image is in the `attachments/`.
* **AI semantic graph** – SentenceTransformers + k-NN → interactive D3 force-directed graph (`/graph`)
![image](https://github.com/user-attachments/assets/aa1ba180-094a-4e77-b560-58b6144f6e3b)
* **GitHub Actions integration** – graph data (`static/data/notes_graph.json`) is rebuilt on **every deploy**
* Fully client-side, no external APIs required at runtime

## 🚀 Quick Start

Follow these five steps to get your own AI-powered knowledge site online in minutes:

1. **Install the theme**
   ```bash
   git submodule add https://github.com/ShawnSWu/Hugo-KidiVerse themes/kidiverse
   # or: hugo mod get github.com/ShawnSWu/Hugo-KidiVerse
   ```

2. **Create your site** (if you don’t have one yet)
   ```bash
   hugo new site my-notes && cd my-notes
   ```

3. **Copy your Markdown notes** into the `content/` folder.  
   • Keep images in each note’s local `attachments/` sub-directory.  
   • Add an `_index.md` to every folder so it appears in the sidebar.

4. **Use the provided sample config**
   ```toml
   baseURL = "https://example.com/"
   languageCode = "en-us"
   title = "My Knowledge Garden"
   theme = "kidiverse"
   themesDir = "themes"
   ```
   Adjust colours & options in `[params]` (see `exampleSite/config.toml`).

5. **Preview locally**
   ```bash
   hugo server -D
   ```
   Visit `http://localhost:1313` – the sidebar, search box and `/graph` page should work out-of-the-box.

➡️ Want the AI graph to stay fresh automatically?  Enable the GitHub Action in the "AI Semantic Graph" section below.

---

## 🤖 AI Semantic Graph

* **Script:** `scripts/build_graph.py`
  * Generates sentence embeddings using `sentence-transformers/all-MiniLM-L6-v2`
  * Computes top-k nearest neighbours with scikit-learn
  * Writes `static/data/notes_graph.json` consumed by the front-end
* **Front-end:** `static/js/graph.js` renders an interactive force graph with D3.

### 🤖 GitHub Actions – Auto-generate the AI Knowledge Graph

The workflow below runs **automatically whenever your note content (`content/**.md`) changes**:

1. Check out your site repository (including the Kidiverse theme if it is included as a Git submodule).
2. Set up Python 3.11 and install the script dependencies.
3. Run `build_graph.py` to generate/update `static/data/notes_graph.json`.
4. Commit the generated file back to the main branch so your deploy (Netlify / Vercel / GitHub Pages …) always contains the latest graph.

Save the snippet below as `.github/workflows/knowledge-graph.yml`:

```yaml
name: Build Knowledge Graph

on:
  push:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  build-graph:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Set up Python 3.12
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Cache pip & model files
        uses: actions/cache@v4
        with:
          path: |
            ~/.cache/pip
            ~/.cache/huggingface
            ~/.cache/torch
          key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Build knowledge graph
        run: python scripts/build_graph.py

      - name: Setup Hugo for site build
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "latest"
          extended: true

      - name: Build site with updated graph
        run: hugo --minify --environment production

      - name: Commit and push changes
        if: success()
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          
          # Add all generated files
          git add static/data/notes_graph.json
          git add static/data/embeddings.npy
          git add static/data/embeddings_index.json
          
          # Check if there are any changes to commit
          if [ -n "$(git status --porcelain)" ]; then
            git commit -m "Update knowledge graph and embeddings [skip ci]"
            git push origin main
          else
            echo "No changes to commit"
          fi

      - name: Commit graph if changed
        uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: 'chore(graph): update notes_graph.json'
          file_pattern: static/data/notes_graph.json

```

> If you pull in Kidiverse via **Hugo Modules** instead of a Git submodule, add a `hugo mod vendor` step right after `actions/checkout` to download the module files before running the script.

Once set up, the graph JSON is regenerated automatically—no manual script runs or file copying needed.

---

## <img src="/static/images/obsidian.svg" width="20" alt="" style="vertical-align: middle;" />  Obsidian Compatibility

Already keeping your notes in an Obsidian vault? Just copy them into `content/` and you’re ready to go:

1. **Drag-and-drop folders** – every folder becomes a sidebar section.
2. **Add an `_index.md`** (Very important, please do it otherwise the folder will not be displayed on your sidebar) in each folder so Hugo can render the section landing page & sidebar entry. 

A minimal example:
   ```markdown
   ---
   title: "Git"   # folder label in sidebar
   weight: 2       # optional ordering (lower = higher)
   ---
   ```

   (You can create these quickly with a script or your file manager.)
3. Keep images in the folder’s `attachments/` sub-directory – the theme will resolve links like `![diagram](diagram.png)` automatically.
4. Start `hugo server ‑D` and enjoy the same Obsidian structure with a searchable website + AI knowledge graph.

---

## 📝 Note-Taking Conventions

1. **Directory = Topic** – organise notes under `content/<topic>/<Note>.md`
2. **Attachments** – place images related to each note in `content/<topic>/attachments/`
3. Insert images simply as:
   ```markdown
   ![my diagram](diagram.png)        # resolves to /<topic>/attachments/diagram.png
   ![[diagram.png]]                  # Obsidian transclusion also works
   ```

---

## ⚙️ Configuration

```toml
baseURL = "https://example.com/"
languageCode = "en-us"
title = "My Knowledge Garden"

[params]
  description = "Personal Zettelkasten powered by KidiVerse"
```

(Advanced parameters such as colours, fonts, sidebar width are documented in `exampleSite/config.toml`.)

---

## 📜 Licence

KidiVerse is released under the **MIT Licence** – see [`LICENSE`](LICENSE).
