# Site

A single-page, dependency-light personal site (Three.js and marked.js are
loaded from a CDN for the background shader and Markdown rendering;
everything else is plain HTML/CSS/JS). Fully static — no build step
required.

## Structure

```
index.html
data/
  projects/
    index.json              order/list of project slugs to load
    distributed-cache.md     one file per project
    static-site-generator.md
    ...
  writing/
    index.json               order/list of writing slugs to load
    zero-dependencies.md      one file per post
    ...
```

Each `.md` file is one entry: a small frontmatter block (metadata) followed
by the Markdown body. `index.html` fetches each `index.json`, then fetches
every `.md` file it lists, parses it, and renders it. The filename (without
`.md`) is the entry's slug and its URL (`#project/distributed-cache`,
`#writing/zero-dependencies`).

If nothing can be fetched (for example, opening `index.html` directly via
`file://` with no server), the page falls back to a small built-in default
data set baked into the script, so it never breaks — it just won't reflect
your Markdown edits until it's served over http.

## Adding a project

1. Create `data/projects/my-new-project.md`:

   ```markdown
   ---
   name: My New Project
   year: 2026
   shortDesc: One-line summary shown in the list.
   thumbnail: media/my-new-project/thumb.jpg
   tech: Rust, WebAssembly, WebGPU
   link: Source → | https://github.com/you/my-new-project
   link: Demo → | https://demo.example.com
   ---

   The rest of the file is the project's full **Markdown** description —
   see below for images and video.
   ```

2. Add `"my-new-project"` to `data/projects/index.json`, wherever you want
   it to appear in the list:

   ```json
   ["my-new-project", "distributed-cache", "static-site-generator"]
   ```

That's it — no other files change.

Frontmatter fields for projects: `name`, `year`, `shortDesc` (plain
one-liner, no Markdown), `thumbnail` (optional), `tech` (comma-separated),
and one `link:` line per link (`Label | URL`) — add as many `link:` lines
as you want, including zero.

## Adding a writing entry

1. Create `data/writing/my-new-post.md`:

   ```markdown
   ---
   title: Post Title
   meta: 2026 — 8 min read
   excerpt: One-line teaser shown in the list.
   thumbnail: media/my-new-post/thumb.jpg
   tags: Engineering, Essays
   ---

   The rest of the file is the post body, in Markdown.
   ```

2. Add `"my-new-post"` to `data/writing/index.json`.

Frontmatter fields for writing: `title`, `meta`, `excerpt` (plain
one-liner), `thumbnail` (optional), `tags` (comma-separated).

## Writing the body in Markdown

The body (everything after the closing `---`) supports full Markdown:
paragraphs, `### headings`, `**bold**` / `*italic*`, lists, `>
blockquotes`, and `` `code` `` / fenced code blocks.

**Images** — standard Markdown image syntax:

```markdown
![Alt text describing the image](media/my-new-project/screenshot.png)
```

**Videos** — Markdown has no native video syntax, so drop in a plain HTML
`<video>` tag; it passes straight through untouched:

```markdown
<video src="media/my-new-project/demo.mp4" controls poster="media/my-new-project/poster.jpg"></video>
```

Mix in as many images/videos with as much text as you want, in any order —
there's no fixed "gallery" limit. Everything renders responsively (scales
to the column width, never overflows).

**Where to put media files** — create a `media/` folder next to `data/`
(e.g. `media/my-new-project/screenshot.png`) and reference it with a
relative path as shown above. Keep file sizes reasonable (compress images,
use a real video codec/container like H.264 mp4) since GitHub Pages serves
everything as static files with no transcoding.

## Previewing locally

Because entries are loaded via `fetch()`, opening `index.html` directly
from disk (`file://...`) will silently fall back to the built-in default
data in most browsers (that's normal `fetch` + `file://` CORS behavior, not
a bug). To see your Markdown edits while developing, serve the folder over
`http://` with any static server, e.g.:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

or `npx serve`, or the VS Code "Live Server" extension — anything that
serves static files works.

## Deploying to GitHub Pages

1. Push this folder to a GitHub repo (commit `index.html`, `data/`, and this
   `README.md`).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`,
   choose your default branch (e.g. `main`) and the `/ (root)` folder.
4. Save. GitHub will publish the site at
   `https://<your-username>.github.io/<repo-name>/` within a minute or two.

From then on, adding a project or post is: add a `.md` file, add its slug to
the matching `index.json`, commit, push — no build step, nothing to
compile.
