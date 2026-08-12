# Khoa Duong — Portfolio

Personal portfolio site, live at **https://khoasdyn.github.io**

Plain HTML and CSS. No build step, no framework, no package manager, no JavaScript.

## Structure

```
index.html          Homepage
glowsary.html       One case study page per project, named for its slug
vincere.html
tailorwing.html
project-04.html
css/style.css       All styles for every page, including the design tokens
assets/
├── fonts/          Two variable WOFF2 files plus their OFL licences
├── images/         Portrait, avatar, and workplace photos per company
├── logos/          Brand and product marks (SVG)
├── duotone-icons/  Only the icons this site uses, kept in category folders
└── project-case-study-images/
    ├── glowsary/       one folder per project; see guidelines for naming
    └── ...
```

Every page shares the one stylesheet. The repo carries just the seven icons the site renders. The full library lives outside the repo so it is not published; copy a single file in, keeping its category folder, when a new one is needed.

## Design system

Everything visual is a CSS custom property in the `:root` block at the top of `css/style.css`.

**Colour** is defined in two layers. Primitives are the raw scales (`--lime-50` … `--lime-950`, `--zinc-50` … `--zinc-950`). Semantic tokens name their purpose (`--brand`, `--surface`, `--text`, `--border`) and point at a primitive. Rules reference the semantic layer only, so rebranding is an edit to one block.

Lime and Zinc are the only palettes. The two footer link labels carry their own third-party brand colours so each matches its logo, and those are the only other colour values in the file.

**Type** is Alan Sans for almost everything and Google Sans Flex for the Download CV button and the footer contact labels. Both are self-hosted variable fonts covering weights 300–900 from a single file each, so there is no external font request and no per-weight `@font-face`. Alan Sans has no Vietnamese glyphs, so any Vietnamese text must be set in Google Sans Flex.

**Sizes and radii** follow the Figma scale: `--text-sm` through `--display-md`, and `--radius-md` through `--radius-full`.

## Case study pages

One page per project, named for the same slug as its image folder. Each is built from four block types, so a page is assembled rather than styled from scratch:

| Block | Markup |
|---|---|
| Paragraphs | `.section.cs-block` wrapping `.cs-prose` |
| Heading | the same, with a `.cs-badge` label above |
| Image | `.cs-figure` holding one `<img>` |
| Block quote | `.section.cs-block.cs-quote` |

Not every section carries a heading. The plain paragraph block exists so a section can continue the previous thought without starting a new one.

The homepage project cards link to these pages. The contact footer is the same markup as the homepage.

Every image slot ships as a real file rather than an empty element, so a slot can be filled later by overwriting the file without touching the HTML. Pages still holding placeholder copy or placeholder images carry `<meta name="robots" content="noindex">` until they are finished.

## Icons

The duotone SVGs are authored with `currentColor`, which an `<img>` cannot inherit. They are painted with a CSS mask instead, so one file works on any background and picks up the surrounding text colour:

```css
.icon    { background-color: currentColor; mask: var(--icon-url) center / contain no-repeat; }
.i-zap   { --icon-url: url("../assets/duotone-icons/general/zap-fast.svg"); }
```

Add an icon by adding an `.i-name` line. Never author or recolour an SVG by hand.

Logos keep their own baked-in colours and stay as plain `<img>`.

## Images

Ship WebP, sized at roughly twice the display size, quality ~82. Project covers cap at 1600px wide and photos at 1000px. Everything below the hero is `loading="lazy"`, the one exception being the cover on a case study page, which is the largest thing above its fold.

**Project case study images:** one folder per project slug under `assets/project-case-study-images/`. Prefix every filename with that slug. Order is `cover` for the homepage card, then `01`, `02`, … for case study pages. Full rules are in `Portfolio Website Guidelines.md` at the workspace root.

One exception: `profile-photo-og.jpg` is a JPEG on purpose. It is the social link preview, and scrapers cannot read WebP.

## Responsive

Breakpoints at 1200, 900, 700 and 480px, shared by every page. The two-up blocks stack at 900, projects go single column at 700, and the contact cards stack. On a case study page the block padding steps down at each breakpoint and the quote's 200px inset collapses to the normal gutter below 700.

## Run locally

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000. Serve it rather than opening the file directly, so paths resolve the way they do in production.

## Deploy

Push to `main`. GitHub Pages rebuilds and the site updates in about 30 seconds.

Pages sends `cache-control: max-age=600`, so a browser can hold the old page for up to 10 minutes after a deploy. Check with `curl` against the live URL, or hard refresh with `Cmd + Shift + R`, before concluding a deploy failed.
