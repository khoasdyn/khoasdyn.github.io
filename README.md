# Khoa Duong — Portfolio

Personal portfolio site, live at **https://khoasdyn.github.io**

Plain HTML and CSS. No build step, no framework, no package manager, no JavaScript.

## Structure

```
index.html          Homepage
glowsary.html       One case study page per project, named for its slug
vincere.html
tailorwing.html
guessify.html
ai-transcription.html   currently hidden; its homepage card is commented out
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

One page per project, named for the same slug as its image folder. Each is built from two block types, so a page is assembled rather than styled from scratch:

| Block | Markup |
|---|---|
| Text | `.section.cs-block` with an `<h2 class="cs-badge">` then `.cs-prose` |
| Image | `.cs-figure` holding one `<img>` or `<video>` |

**Every text block carries a heading.** There is no unheaded variant and no block quote; both were removed in August 2026. An unlabelled slab of text after a card break reads as disconnected from what came before it, so a block without a heading is either the tail of the previous section or a new idea that has not been named yet. The badge is a real `<h2>`, which is what gives each page its heading outline.

Text blocks cap at `--measure` (800px) and centre inside the card. Figures stay full-bleed and as tall as their own media, so nothing is ever cropped. The one exception is the cover, which takes `.cs-cover` and matches the homepage card's 16:10 box.

The homepage project cards link to these pages. The contact footer is the same markup as the homepage.

Every image slot ships as a real file rather than an empty element, so a slot can be filled later by overwriting the file without touching the HTML. A page carries `<meta name="robots" content="noindex">` while it still holds placeholder copy or images, or while it is deliberately hidden from the homepage.

## Icons

The duotone SVGs are authored with `currentColor`, which an `<img>` cannot inherit. They are painted with a CSS mask instead, so one file works on any background and picks up the surrounding text colour:

```css
.icon    { background-color: currentColor; mask: var(--icon-url) center / contain no-repeat; }
.i-zap   { --icon-url: url("../assets/duotone-icons/general/zap-fast.svg"); }
```

Add an icon by adding an `.i-name` line. Never author or recolour an SVG by hand.

Logos keep their own baked-in colours and stay as plain `<img>`.

## Images

Ship WebP, never the raw PNG or JPEG. Photos are sized at roughly twice their display size at quality ~82, capped at 1000px.

**Case study media is lossless WebP at the source's own native size, with no resize step.** Lossless WebP is pixel-for-pixel identical to the source PNG and smaller than it. The old 1600px cap was the reason earlier pages looked soft: the case study card is 1416 CSS pixels wide, which is 2832 real pixels on a retina screen, so a 1600px file was being blown up almost 2x. The cost is weight, and Guessify is the heaviest page at 9.5 MB.

Every `<img>` and `<video>` carries `width` and `height` set to the file's real pixel size, because a body figure has no fixed height and a lazy image without them is zero pixels tall until it loads. Everything below the hero is `loading="lazy"`, the one exception being the cover on a case study page, which is the largest thing above its fold.

Screen recordings are transcoded to H.264 (an iPhone capture is H.265, which Chrome and Firefox handle inconsistently), autoplay muted on a loop, and carry a WebP `poster`. A portrait recording is centred on a solid colour inside a 16:9 frame rather than stretched full-bleed.

**Project case study images:** one folder per project slug under `assets/project-case-study-images/`. Each file keeps the name it was exported under, lowercased; `cover` is the homepage card. Full rules are in `Portfolio Website Guidelines.md` at the workspace root.

One exception: `profile-photo-og.jpg` is a JPEG on purpose. It is the social link preview, and scrapers cannot read WebP.

## Responsive

Breakpoints at 1200, 900, 700 and 480px, shared by every page. The two-up blocks stack at 900, projects go single column at 700, and the contact cards stack. On a case study page the block padding steps down at each breakpoint. Below roughly 860px the card is narrower than the 800px measure, so `--pad-x` takes over on its own and no extra breakpoint is needed.

## Run locally

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000. Serve it rather than opening the file directly, so paths resolve the way they do in production.

## Deploy

Push to `main`. GitHub Pages rebuilds and the site updates in about 30 seconds.

Pages sends `cache-control: max-age=600`, so a browser can hold the old page for up to 10 minutes after a deploy. Check with `curl` against the live URL, or hard refresh with `Cmd + Shift + R`, before concluding a deploy failed.
