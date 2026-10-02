# Dark Hour — student e-portfolio

One file, no build step, no dependencies to install. Open `index.html` in a browser to
view it; open it in any text editor to change it.

## Where the content lives

Everything you will want to change sits in a single object called `PORTFOLIO`, near the
top of the `<script>` at the bottom of `index.html`. Search the file for
`EDIT EVERYTHING HERE` and start there. Below that object there is a line saying
*"Below this line is the machinery"* — you never need to touch anything after it.

The object has four parts:

| Part | What it holds |
| --- | --- |
| `profile` | Your name, role, school, location, tagline, email, CV link, photo, and the four facts in the strip under the cover |
| `order` | Which sections appear, and in what order. Delete a name to hide that section. |
| `sections` | The content of each section |
| `footer` | The "updated" line, and the template credit (delete it once the site is yours) |

## Adding your own entries

Each section declares a `kind`, which decides how its items are drawn:

- **`prose`** — a column of paragraphs. Uses `paragraphs: ["...", "..."]`.
- **`records`** — a dated list, used for schools and activities. Each item takes
  `when`, `whenNote`, `title`, `org`, `detail`, `bullets: []`, and `grades: [{k, v}]`.
  Any of those can be left out.
- **`projects`** — the cards that open a detail panel. Each item takes `code`, `year`,
  `title`, `blurb`, `tags: []`, and a `detail` object holding `blocks: [{heading, body,
  bullets}]` and `links: [{label, url}]`.
- **`skills`** — `groups: [{group, items: []}]`.
- **`awards`** — `items: [{year, title, org}]`.
- **`contact`** — `primary: {label, text, url}` is the large line at the top of the
  section (omit it and `profile.email` is used instead), plus `note2` and
  `links: [{label, url, hint}]`.

A link whose `url` is `""` or `"#"` is treated as "not uploaded yet" and is not
rendered at all, in the contact row and inside project panels alike — so you can
leave placeholders in the config as reminders without shipping dead links. Links
to other sites open in a new tab; `mailto:`, `tel:` and files next to the page
open in the same one.

To add a whole new section: invent a key (say `research`), add an entry for it under
`sections` with a `kind`, `nav`, `title` and `note`, then add `"research"` to `order`.

## A few fields accept light HTML

The tagline, the `about` paragraphs, project blurbs, `body` text and bullets are passed
through as written, so you can use:

```html
<em>italic</em>   <strong>bold</strong>
<span class='hi'>accent glow behind the words</span>   <span class='pen'>accent text</span>
```

Use single quotes there, since the surrounding JavaScript string uses double quotes.
Everything else (names, dates, grades, tags) is escaped automatically, so an `&` or a
`<` in a title is safe.

## Photo

Put a picture next to `index.html` and set `profile.photo: "photo.jpg"`. Leave it as `""`
and you get the ruled monogram plate instead, drawn from `profile.initials`.

Two fields control the frame:

- `photoAspect` — the frame's shape, default `"4 / 5"`. Set it to `"1 / 1"` for a square,
  `"3 / 4"` for a taller frame, or `"auto"` to let the photo keep its own proportions
  with no cropping at all.
- `photoFocus` — which part of the photo survives the crop, default `"50% 30%"`
  (horizontal, then vertical). Lower the second number to keep a face that sits high in
  the frame; `"50% 22%"` is what this site uses.
- `photoHover` — a second image, shown the instant the frame is hovered and gone
  the instant you leave. Both images are in the page from the start, so the swap
  never waits on a download, and there is no fade: it cuts straight over. Leave it
  as `""` for no swap. If the file is not there, the page quietly drops it and just
  shows the first photo.
- `photoHoverFocus` — the crop centre for that second image. Leave it empty and it
  crops exactly like `photoFocus`, which is what you want when both files are the
  same size and framed the same way. Only set it if the second image needs its own
  crop; a *smaller* percentage shows more of the top of the image, which pushes the
  subject further down the frame.

## The two themes

One toggle, top-right of the nav, and the choice is remembered in the browser.

| | Ground | Accent | Text |
| --- | --- | --- | --- |
| **Dark** | `060B1C` midnight | `6B93FF` royal blue | `EEF3FF` |
| **Light** | `EDF1FA` pale | `0B4FD6` dark blue | `0D1633` |

With nothing chosen the page follows the viewer's system setting. To force one for
everybody, set `theme.default` in the config to `"dark"` or `"light"`; a visitor's
own toggle still wins over it. Printing always uses plain black on white.

Each theme is one block of CSS variables at the top of the `<style>`, under
`/* ---- 1. Palettes */`. There are three copies of the light values, and all three
have to agree:

1. `:root[data-theme="light"]` — when the toggle chose light
2. `@media (prefers-color-scheme: light) :root:not([data-theme="dark"])` — a light
   system with no choice made
3. the `@media print` block

The dark values live in the bare `:root`, so dark is the default.

The tokens that matter: `--bg` (page ground), `--surface` / `--surface-2` (panels),
`--ink` / `--ink-2` / `--muted` (three levels of text), `--line` / `--line-soft`
(borders), `--acc` (the accent), `--acc-ink` (text that sits *on* the accent),
`--moon`, and `--glow`.

## Typefaces

From Google Fonts, in one `<link>` in the `<head>`:

- **Barlow Condensed** — headings, names, buttons and section titles, mostly
  italic and uppercase. This is what carries the Persona look.
- **Barlow** — body text.
- **IBM Plex Mono** — dates, subject codes, grades and small labels.

## The visual devices, and where they come from

- `--skew` (default `-9deg`) leans the nav, buttons, chips, grades, tags and the
  Arcana plates. Anything that leans has an inner element leaning back the other
  way, so the text inside stays upright. Set `--skew: 0deg` to square everything.
- Sections are numbered with Roman numerals, like Arcana, and the numeral appears
  again huge and faint behind the section, drifting as you scroll.
- Cards and the project panel have a notched corner, cut with `clip-path`.
- Your name is printed twice: the second copy is a solid accent shadow, offset
  just under a stroke width so no ground shows between a letter and its shadow.
- **The nav indicator** travels at a constant speed rather than a constant
  duration. A fixed duration made a hop to the neighbouring section crawl and a
  jump across the whole bar race, because the sections are different widths, so
  the script sets the transition duration from the distance instead
  (`IND_SPEED`, 0.55px per ms). The floor and ceiling are guards for odd cases
  and do not engage in normal use. A resize repositions it instantly.

  Its resting state is a `IND_HOME_W` line (4px) standing on the left edge of the
  bar, and that is how the page paints: on screen from the first frame, nothing
  fading in, nothing waiting. When a section becomes current the line simply
  slides out of the edge and widens to sit under that link. Clicking your name
  sends it back: no section is current any more, so it travels home and collapses
  to the line again, at the same constant speed as any other move. Scrolling up
  past the first section does the same thing, because the observer tracks which
  sections are inside the reading band rather than only reacting to arrivals —
  at the top that set is empty, and an empty set means home. When two sections
  share the band the later one wins, since that is the one being scrolled into.

  The foot of the page is handled separately, because the band stops moving once
  you cannot scroll any further: on a tall window the last section can sit
  entirely below the band and never register at all. So reaching the bottom of
  the scroll range always reads as the last section in `order`, whatever the band
  says. That edge is watched with a plain scroll listener — crossing it changes no
  intersection, so the observer alone would never hear about it.

  A click also *owns* the trip. The page smooth-scrolls past every section in
  between, and the section observer would report each one in turn, which made the
  slab hop from link to link on the way down. So a click points the indicator
  straight at its target and the observer stands down until the scrolling settles
  — on `scrollend` where the browser has it, otherwise once the scrolling has been
  idle for 160ms, and immediately if the reader grabs the wheel or the keyboard.
- **Hover.** One blue shadow, defined once at the top of the CSS and used by
  everything you can point at: project cards, record rows, fact tiles, skill
  chips, links, buttons, award rows and the photo frame. It comes in two forms
  because `clip-path` clips `box-shadow` away entirely — `--lift` is the
  box-shadow version for ordinary boxes, and `--lift-f` is the same thing as a
  `filter: drop-shadow()`, which applies *after* the clip and so traces the
  notched corner on the cards and the photo frame. Both resolve against whichever
  `--acc` the theme has, so one definition covers light and dark. It is a hard
  graphic shadow rather than a glow: solid accent at full opacity, zero blur,
  offset equally in x and y (`.45rem` each) so it falls at 45 degrees toward the
  bottom right. A third token, `--lift-deep`, is the same shadow in a darker shade
  of the accent, used by the two hovers that fill *themselves* with the accent
  (skill chips and the panel close button) so the shadow stays readable against
  the fill instead of merging into it.
  Change the offset number in the tokens to push the shadow further out or
  pull it closer in.
- **The cursor.** Two followers. A dot written straight from the pointer event, so
  it has no lag at all. A ring that eases toward the pointer at 17% a frame and is
  then *clamped*: if the gap ever exceeds the ring own radius it snaps back, so the
  dot can never leave the ring however fast you move. Over anything clickable the
  ring opens up and the dot tucks in. Tune it in the cursor block of the script:
  `0.17` for how quickly the ring catches up, `MAX_GAP` for how loosely it sits
  around the dot. It only runs on a fine pointer, and the native cursor is hidden
  only once you have actually moved the mouse.
- **The background.** A gradient ground: a vertical fall from `--bg-2` through
  `--bg` and back, with two washes over it — an accent wash up under the moon and
  a deeper `--acc-2` wash bottom-left. The washes **drift**, on 52s and 67s cycles
  that hold still at each end and only move through the middle, with a slow opacity
  breath on separate 41s and 53s cycles so the two never sync. Nothing else moves
  back there. Over the top, still scanlines at about 9% opacity, and the moon,
  large and top-right. It is theme-specific: pale cool blue on the dark theme,
  a soft light yellow on the light one (`--moon`, with `--moon-shade` for its
  inner shading and `--moon-alpha` for how present it is — .3 dark, .6 light).
  The light theme shades it warm rather than black, since black drains the hue
  and leaves grey on a pale ground.
- **Parallax**, two layers: the moon (−0.22) and a perspective floor grid under
  the footer (−0.07). The section numerals drift as they cross the viewport. All
  transform-only, and all of it stops under reduced motion.

Where to tune the background: `--moon` in each palette for the disc colour and
`.px-moon { opacity }` (0.3) for how present it is; the two `color-mix` percentages
on `.wash-a` / `.wash-b` (20% and 24%) for how strong the washes are; the
`@keyframes wash-drift-a` / `wash-drift-b` percentages for how much of each cycle
is movement rather than hold — widen the `0%,16%` and `84%,100%` stops to make it
sit still longer. `.tex-scan` carries the scanline opacity as a multiple of `--tex`.

## What else is built in

- Two themes on one toggle, remembered per visitor
- Scrollbars are hidden everywhere (`scrollbar-width` plus `::-webkit-scrollbar`);
  the page still scrolls normally by wheel, touch, keyboard and drag
- A link with no `url` yet renders as a dashed "soon" slot instead of vanishing or
  going nowhere — fill the `url` in and it becomes a real link
- `Ctrl/Cmd + P` prints as plain black on white — the skew, glow, textures,
  parallax and navigation all drop away
- Project panels can be dragged down to dismiss, with real momentum, and honour
  `prefers-reduced-motion`, `prefers-reduced-transparency` and `prefers-contrast`
- Keyboard accessible throughout: skip link, visible focus rings, `Esc` closes a panel
- Works from a file, a USB stick, GitHub Pages, or any static host

## Publishing it

Rename nothing. Upload `index.html` (plus your photo and CV) to any static host —
GitHub Pages, Netlify, Cloudflare Pages, or your school's web space. There is no build
step, so the folder you edit is the folder you upload.
