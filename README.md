# Khoa Duong — Portfolio

Personal portfolio site, live at **https://khoasdyn.github.io**

Plain HTML and CSS. No build step, no framework, no package manager, no JavaScript.

## Structure

```
index.html          The whole page, one file
css/style.css       All styles, including the design tokens
assets/
├── fonts/          Two variable WOFF2 files plus their OFL licences
├── images/         Portrait, avatar, and workplace photos per company
├── logos/          Brand and product marks (SVG)
├── duotone-icons/  Only the icons this site uses, kept in category folders
└── project-case-study-images/
    ├── glowsary/       one folder per project; see guidelines for naming
    └── ...
```

The repo carries just the six icons the page renders. The full library lives outside the repo so it is not published; copy a single file in, keeping its category folder, when a new one is needed.

## Design system

Everything visual is a CSS custom property in the `:root` block at the top of `css/style.css`.

**Colour** is defined in two layers. Primitives are the raw scales (`--lime-50` … `--lime-950`, `--zinc-50` … `--zinc-950`). Semantic tokens name their purpose (`--brand`, `--surface`, `--text`, `--border`) and point at a primitive. Rules reference the semantic layer only, so rebranding is an edit to one block.

Lime and Zinc are the only palettes. The two footer link labels carry their own third-party brand colours so each matches its logo, and those are the only other colour values in the file.

**Type** is Alan Sans for almost everything and Google Sans Flex for the Download CV button and the footer contact labels. Both are self-hosted variable fonts covering weights 300–900 from a single file each, so there is no external font request and no per-weight `@font-face`. Alan Sans has no Vietnamese glyphs, so any Vietnamese text must be set in Google Sans Flex.

**Sizes and radii** follow the Figma scale: `--text-md` through `--display-md`, and `--radius-md` through `--radius-full`.

## Icons

The duotone SVGs are authored with `currentColor`, which an `<img>` cannot inherit. They are painted with a CSS mask instead, so one file works on any background and picks up the surrounding text colour:

```css
.icon    { background-color: currentColor; mask: var(--icon-url) center / contain no-repeat; }
.i-zap   { --icon-url: url("../assets/duotone-icons/general/zap-fast.svg"); }
```

Add an icon by adding an `.i-name` line. Never author or recolour an SVG by hand.

Logos keep their own baked-in colours and stay as plain `<img>`.

## Images

Ship WebP, sized at roughly twice the display size, quality ~82. Project covers cap at 1600px wide and photos at 1000px. Everything below the hero is `loading="lazy"`.

**Project case study images:** one folder per project slug under `assets/project-case-study-images/`. Prefix every filename with that slug. Order is `cover` for the homepage card, then `01`, `02`, … for case study pages. Full rules are in `Portfolio Website Guidelines.md` at the workspace root.

One exception: `profile-photo-og.jpg` is a JPEG on purpose. It is the social link preview, and scrapers cannot read WebP.

## Responsive

Breakpoints at 1200, 900, 700 and 480px. The two-up blocks stack at 900, projects go single column at 700, and the contact cards stack.

## Run locally

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000. Serve it rather than opening the file directly, so paths resolve the way they do in production.

## Deploy

Push to `main`. GitHub Pages rebuilds and the site updates in about 30 seconds.

Pages sends `cache-control: max-age=600`, so a browser can hold the old page for up to 10 minutes after a deploy. Check with `curl` against the live URL, or hard refresh with `Cmd + Shift + R`, before concluding a deploy failed.
