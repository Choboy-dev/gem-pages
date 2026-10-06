# Happy Dog Bites — nutrition page sections

My technical test for the Customer Success Associate role at GemCommerce. I built three sections from the Figma page as a live HTML/CSS page, and turned the main one (Nutrition highlights) into a Shopify section that a merchant can edit without touching code.

## Overview

The page has three blocks, all from the Figma "Home page" frame:

1. **What makes us different** — centred heading, four features around a bowl photo, a button, the guarantee line and payment icons.
2. **Nutrition highlights** — the main section for the test. Heading, paragraph, three percentage stats, a button, and the photo of the dog with the Happy Dog Bites bag.
3. **Digestive health** — two image/text rows that swap sides.

All colours, font sizes and spacing live in one place at the top of `styles.css`, and every section uses them. The Shopify file has its own copy of the values it needs, so it still looks right when added to any theme.

The buttons in Figma don't have a link, so for now they point to `#`. Search for `TODO` to find them.

## Live demo + repo links

- Live demo: [choboy-dev.github.io/gem-pages](https://choboy-dev.github.io/gem-pages/)
- Repository: [github.com/Choboy-dev/gem-pages](https://github.com/Choboy-dev/gem-pages)

Hosted on GitHub Pages from the root of the `main` branch.

## File structure

```
gem-pages/
├── index.html          The page
├── styles.css          All styles and design values
├── .nojekyll           Tells GitHub Pages to serve the files as-is
├── assets/             Photos, icons, payment logos, favicon
└── shopify/
    └── sections/
        └── nutrition-highlights.liquid
```

I didn't need any JavaScript. Hover, focus and reduced-motion are all handled in CSS.

## HTML structure and semantic choices

Everything sits inside one `<main>`, and each block is a `<section>`.

- The first two sections use `aria-labelledby` pointing to their `<h2>`, so screen reader users can jump straight to them by name.
- The third section has two `<article>`s, each with its own `<h2>`, so I gave the section an `aria-label` ("Digestive health") instead.
- Feature titles are `<h3>` since they sit under the section heading.
- The features, stats and payment icons are all `<ul>` lists, because they're groups of similar items.
- "Key Points:" labels the stats list through `aria-labelledby`.
- Buttons are `<a>` tags styled as buttons, since they take you somewhere.
- Photos have alt text describing what's in them. The small feature icons have empty alt text because the title next to them already says what they mean.

## CSS approach

The custom properties at the top of `styles.css` hold the colours, font, type sizes, line heights, letter spacing, the 1170px container, the 570px column and the corner radii. For example, changing `--color-accent` updates the percentages, both buttons and the hover colour at once.

Class names follow BEM: `nutrition` is the block, `nutrition__stat` is an element, `benefit--media-first` is a modifier. No frameworks.

Layout is just Flexbox and Grid. The 1170px container comes straight from the Figma frame: two 570px columns with a 30px gap.

## Responsive strategy

I wrote it mobile-first, with two breakpoints:

| Width | What changes |
| --- | --- |
| Under 768px | Single column. Headings scale with `clamp()`. Buttons are full width. 20px side padding, no horizontal scroll down to 320px. |
| 768px – 1199px | Nutrition and digestive rows go two columns with a 24px gap. The "different" row keeps its three columns with a smaller bowl in the middle. |
| 1200px and up | Matches Figma: 40px headings, 33px percentages, 30px column gap, 80px section padding, 48px between heading group and stats, 32px padding on the text column. |

Headings use `clamp()` so they scale down smoothly on smaller screens, and they max out at the Figma size (40px) on desktop.

**Why the image goes below the text on mobile:** the heading and stats are the message of each section, so I wanted people to read them and see the button before scrolling past a big photo. On tablet and up, the first digestive row moves its photo back to the left (like in Figma) using CSS `order`, while the HTML keeps the text first for screen readers.

## Accessibility

- Heading text (`#161723`) on white is about 17.8:1 and body text (`#424153`) is about 9.9:1 (9.4:1 on the grey background). Both pass WCAG AA.
- Photos have alt text, decorative icons don't.
- Buttons and the skip link get a dark outline with `:focus-visible`, so keyboard users can see where they are.
- On hover the button gets a bit darker and lifts 1px. If the user has reduced motion turned on, the lift and transition are removed (the colour change stays).
- **One contrast issue I left in on purpose.** The Figma accent `#EE6F4B` with white text is only **3.00:1**, and 16px text needs **4.5:1** to pass AA. The percentages on the grey background are **2.82:1**, which is below the 3:1 needed for large text. I kept the original colour so the page matches the design, but I'd suggest **`#BC583B`** instead. It's the same hue and gets about 4.57:1 with white text. In the Shopify section that's a one-line change to `--color-accent`.

## Performance

- Photos are WebP, exported at 2x, with a smaller version in `srcset` for phones.
- Every image has `width` and `height` set, so nothing jumps around while loading.
- The bowl image is near the top so it loads straight away. Everything further down uses `loading="lazy"`.
- No JavaScript, no framework. Icons are small SVGs exported from Figma.
- I didn't use `<picture>` because all the images are already WebP and `srcset` covers the sizes.

## Shopify section

The file is `shopify/sections/nutrition-highlights.liquid`. It's the Nutrition highlights block.

Settings available in the theme editor:

| Setting | What it does |
| --- | --- |
| Heading | The main heading |
| Body text | The paragraph under it |
| Key points label | The "Key Points:" text. Leave empty to hide it. |
| Image | The photo, picked from the store's files |
| Button label | The button text. Leave empty to hide the button. |
| Button link | Where the button goes |
| Background | Section background colour (`#F8F8F8` in the design) |
| Padding top / bottom | Space above and below, 0–120px (80px in the design) |
| Image position | Right (as in the design) or left |

Each stat is a **Stat** block with a percentage and a short text, so merchants can add, remove or reorder them. The divider lines between stats are added automatically. The section has a preset, so when you add it from **Add section** it already comes with the three stats from the design.

The image uses Shopify's `image_url` and `image_tag` filters, which output a `srcset`, `sizes` and lazy loading. If the merchant hasn't added alt text to the image, the heading is used as a fallback.

All the CSS is scoped to `#shopify-section-{{ section.id }}`, so you can add the section twice on the same page without the two clashing, and it won't affect the rest of the theme.

On mobile the text stays above the image. The left/right setting applies from 768px up.

The section loads Inter Tight so it matches the design on any theme. If the store has its own brand font, I'd move the font link into `theme.liquid` and put the theme font first in `--font-family`.

## Design decisions & assumptions

- I used the values straight from Figma: background `#F8F8F8`, accent `#EE6F4B`, title `#161723`, body `#424153`, divider `#E3E3E8`, Inter Tight, 6px button radius, 10px image radius, 80px vertical padding, 30px column gap.
- The container is 1170px (570 + 30 + 570), which is how the Figma frame is laid out.
- Buttons link to `#` because the design doesn't include a URL.
- I kept the accent colour as designed even though it fails contrast. The suggested fix is above.
- Payment icons are the five from the design, in the same order: PayPal, Visa, Mastercard, Apple Pay, Google Pay.
- I added a simple coral favicon so the browser tab isn't blank. It isn't from the design.

## How to run locally

It's a static site. From the project folder:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`. Opening `index.html` directly also works, but a local server is closer to how GitHub Pages serves it.

The font loads from Google Fonts, so you need an internet connection. Without it, the page falls back to Inter or the system font.

## How to install the Liquid section in a theme

1. In Shopify admin, go to **Online Store → Themes**.
2. On your theme, click **⋯ → Edit code**. If the store is live, duplicate the theme first and work on the copy.
3. Under **Sections**, click **Add a new section**.
4. Name it `nutrition-highlights`.
5. Replace everything in the new file with the contents of `shopify/sections/nutrition-highlights.liquid` from this repo.
6. Click **Save**.
7. Go back and click **Customize** on the theme.
8. On the page you want, click **Add section** and pick **Nutrition highlights**.
9. Upload the dog photo (`assets/dog-nutrition.webp` in this repo), add the button link, and adjust the padding or image side if needed.

The section includes its own styles, so you don't need any extra CSS files. To remove it, delete the section in the customizer or delete the file from the theme code.
