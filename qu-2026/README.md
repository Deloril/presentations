# Terminal deck theme

The lpearson.co look as a presentation template. Write slides in Markdown, style
them with one CSS file, export to PDF, HTML or PPTX. No PowerPoint needed.

Files:

- `lpearson-terminal.css`: the theme (palette, fonts and terminal motifs from the site)
- `sample-deck.md`: a worked example showing every slide type

## Fastest path: VS Code

1. Install the **Marp for VS Code** extension.
2. Add the theme in settings (`.vscode/settings.json` or user settings):
   ```json
   { "markdown.marp.themes": ["./presentation/lpearson-terminal.css"] }
   ```
3. Open `sample-deck.md` and hit the preview button. Edit, watch it update live.
4. Export from the command palette: **Marp: Export slide deck** (PDF, PPTX, HTML, PNG).

## Command line

```bash
# one-off, no install:
npx @marp-team/marp-cli sample-deck.md --theme ./lpearson-terminal.css -o deck.pdf

# or install once:
npm i -g @marp-team/marp-cli
marp sample-deck.md --theme ./lpearson-terminal.css -o deck.pdf   # PDF for the stage
marp sample-deck.md --theme ./lpearson-terminal.css -o deck.pptx  # if a venue demands PPTX
marp sample-deck.md --theme ./lpearson-terminal.css --html -o deck.html
marp -s .   # live preview server for the whole folder
```

PDF is the safe choice for presenting: no live JavaScript, no font surprises on
someone else's laptop.

## Writing slides

Slides are separated by `---`. Front matter at the top sets the theme:

```markdown
---
marp: true
theme: lpearson-terminal
paginate: true
size: 16:9
---
```

Per-slide layouts, set with a comment at the top of a slide:

- `<!-- _class: lead -->` title slide (big heading, optional `> $ prompt` eyebrow)
- `<!-- _class: section -->` section divider (mono kicker + big heading)
- `<!-- _class: end -->` closing slide
- `<!-- _class: dense -->` smaller type when a slide is content-heavy
- `<!-- _class: two-up -->` two images side by side (see below)
- `<!-- _class: split -->` text one side, full-height image the other (see below)
- `<!-- _paginate: false -->` hide the page number on that slide

Handy patterns:

- `**bold**` renders in cyan, so use it for the word you want to pop.
- A `> $ some command` line on a lead slide becomes the mono eyebrow above the title.
- Fenced code blocks get a terminal titlebar (the three dots) automatically.
- `- bullets` get a cyan `>_`; numbered lists get zero-padded `01, 02, ...`.
- A ` ```console ` block distinguishes a prompt from its response. Prompt lines
  start with `$ ` (or `> `) and render cyan; every other line is the bright-white
  response. A `#` comment renders green and bold (put it after a command, or on
  its own prompt as `> # note`, since a bare `#` reads as a root prompt):
  ```console
  > cat definition_of_incident.salesforce   # the working definition
  An event that compromises the CIA of a system ...
  ```
  Use ` ```console `, not ` ```bash ` — only `console` marks up the prompt.

## Images

- **One image.** Size it with a keyword in the alt text: `![w:600](diagram.png)`
  (`w:` width, `h:` height, both accept px or `%`). Paths are relative to the `.md`.
- **Caption.** Put an `*emphasised*` line directly under the image, with no blank
  line between them. It renders as a centred mono caption beneath the image:
  ```markdown
  ![w:600](diagram.png)
  *Figure 1: the DAIR model*
  ```
- **Two side by side** (`<!-- _class: two-up -->`). Both images on one line with a
  space between; they centre with an even gap and a subtle frame:
  ```markdown
  <!-- _class: two-up -->
  ![w:520](before.png) ![w:520](after.png)
  ```
- **Text one side, image the other** (a split). Use a background image with
  `bg right` (or `bg left`); the heading stays top-left and the body centres
  itself vertically beside the image. No class needed. Change the ratio with
  `bg right:40%`:
  ```markdown
  # The finding
  ![bg right](evidence.png)
  - what we saw
  - what it meant
  ```

## Other tools, if you outgrow Marp

- **Slidev** (`sli.dev`): Vue-based, built for developers. Code walk-throughs with
  line highlighting, click-through animations, presenter mode with a webcam inset.
  Reach for it when you want motion and interactivity and don't mind Node tooling.
- **reveal.js**: hand-written HTML. Reuse the site's exact CSS and JS, including the
  typing animation. Most control, most effort. Good if a talk is basically a web page.
