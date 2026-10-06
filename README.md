# Happy Dog Bites — nutrition page sections

This is a Customer Success technical test for GemCommerce. It turns three sections from the Figma page into a live HTML page, and turns the middle section into a Shopify theme section a merchant can edit without touching code.

## Overview

The page is three blocks from the Figma frame “Home page”:

1. **What makes us different** — a centred heading, four short features around a bowl photo, a button, a guarantee line, and payment marks.
2. **Nutrition highlights** — the required section. A heading, a paragraph, three percentage stats, a button, and a photo of a dog with a bag of Happy Dog Bites.
3. **Digestive health** — two rows that alternate photo and text.

The same colours, type sizes, and spacing are stored once, at the top of `styles.css`, and reused by every section. The Shopify file repeats the nutrition tokens so the section still looks right when it is dropped into a theme on its own.

The buttons in the Figma file are not links. On this page they point at `#` until a real product URL is added. Search the project for `TODO` to find them.

## Live demo + repo links

- Live demo: [choboy-dev.github.io/gem-pages](https://choboy-dev.github.io/gem-pages/)
- Repository: [github.com/Choboy-dev/gem-pages](https://github.com/Choboy-dev/gem-pages)

The site is published by GitHub Pages from the root of the `main` branch.

## File structure

```
gem-pages/
├── index.html          The page
├── styles.css          All layout and visual tokens
├── .nojekyll           Tells GitHub Pages to publish the files as they are
├── assets/             Photos, icons, payment marks, favicon
└── shopify/
    └── sections/
        └── nutrition-highlights.liquid
```

There is no `script.js`. Hover, focus, and the reduced-motion setting are all done in CSS.

## HTML structure and semantic choices

The page is one `<main>`. Each block is a `<section>`.

- The first two sections are named with `aria-labelledby`, pointing at their `<h2>`. A screen reader can then jump to “What makes us different” or “Nutrition is the foundation…”.
- The third section holds two articles, each with its own `<h2>`, so it uses `aria-label="Digestive health"` instead of borrowing one of those headings.
- Feature titles are `<h3>` elements, because they sit under the section heading.
- The four features, the three stats, and the payment marks are lists (`<ul>`). They are groups of peer items, not loose boxes.
- The “Key Points:” line labels the stat list with `aria-labelledby`.
- Every call to action is an `<a>` styled as a button, not a `<div>` with a click handler.
- Photos have a description of what is in the picture. Icons next to a visible title are decorative, so their alt text is empty and they are not announced twice.

## CSS approach

Custom properties at the top of `styles.css` hold every shared decision: colours, the font stack, type sizes, line height, letter spacing, the 1170px content width, the 570px column, and the corner radii. Changing the accent colour in that one place updates the percentages, both buttons, and the hover colour.

Class names follow BEM. A block is `nutrition`, an element is `nutrition__stat`, and a modifier is `benefit--media-first`. There is no Bootstrap, Tailwind, or other framework.

Layout uses Flexbox and Grid only. The desktop content width is 1170px, which is what the Figma frame uses inside the 1920px artboard (570 + 30 + 570).

## Responsive strategy

The page is written mobile-first. Three stops:

| Width | What changes |
| --- | --- |
| Under 768px | One column. Type uses `clamp()` so headings shrink smoothly instead of jumping. Buttons are full width. Page padding is 20px, so nothing needs a sideways scroll at 320px. |
| 768px to 1199px | The nutrition and digestive rows stay two columns, with a smaller gap (24px instead of 30px). The “what makes us different” row stays three columns, with a smaller bowl, so the photo remains in the centre. |
| 1200px and up | Matches the Figma frame: 40px semibold headings, 33px bold percentages, 30px column gap, 80px section padding, 48px between the nutrition heading group and the stats, 32px of padding on the side of the text that faces the photo. |

Headings use `clamp()` below 1200px and stop at the Figma size (40px) once the screen is wide enough. That is why a heading can be fluid and still match the file on a desktop.

**Why the photo sits under the text on a phone.** These blocks are arguments, not a hero banner. The heading is the point of the section. Putting it first means a person sees the claim, the proof, and the button before a tall photo pushes them down the page. From 768px up, the digestive row that has its photo on the left in Figma moves the photo back to the left with CSS `order`. The heading stays first in the HTML for screen readers.

## Accessibility

- Text colour `#161723` on white is about 17.8:1. Body text `#424153` on white is about 9.9:1, and about 9.4:1 on the grey section. Both pass WCAG AA.
- Photos have useful alt text. Decorative icons do not.
- Buttons and the skip link show a dark outline on `:focus-visible`, so mouse users do not see a ring and keyboard users do.
- The button darkens and lifts by 1px on hover. If the visitor has asked the system to reduce motion, the lift and the transition are turned off. The colour change stays, because that is not movement.
- **Contrast problem, kept on purpose.** The Figma accent is `#EE6F4B`. White button text on that colour is **3.00:1**. Normal text (this button is 16px) needs **4.5:1**, so it fails WCAG AA. The same colour on the grey background (`#F8F8F8`) is **2.82:1**, so the big percentages fail the 3:1 bar for large text as well. The page still uses `#EE6F4B`, because the test asks for the Figma colour. The closest replacement on the same hue that passes is **`#BC583B`** (about 4.57:1 for white text). In the Shopify section this is one line: `--color-accent`.

## Performance

- Photos are WebP, exported at 2x for sharp screens, with a smaller file in `srcset` for phones. The browser picks one.
- Every photo has `width` and `height`, so the layout does not jump when the file arrives.
- The bowl is the first large image, so it loads immediately. The photos lower on the page use `loading="lazy"`.
- There is no JavaScript and no CSS framework. Icon files are the small SVGs from the Figma frame.
- A `<picture>` element was not needed: every photo is already WebP, and `srcset` is enough to offer two sizes.

## Shopify section

The file is `shopify/sections/nutrition-highlights.liquid`. It is the nutrition block only, which is the section the brief asked for as a theme section. The other two blocks stay on the HTML page.

What a merchant can edit in the theme editor:

| Setting | What it changes |
| --- | --- |
| Heading | The `<h2>` |
| Body text | The paragraph under the heading |
| Key points label | The “Key Points:” line. Clear it to hide the label. |
| Image | The photo, chosen with the image picker |
| Button label | The button words. Clear it to hide the button. |
| Button link | Where the button goes |
| Background | The section colour. The design uses `#F8F8F8`. |
| Padding top / padding bottom | Space above and below the section, 0–120px. The design uses 80 and 80. |
| Image position | Right (the design) or left |

Each percentage is a **Stat** block, with a percentage and a sentence. Merchants can add, delete, or reorder stats. The line between stats is drawn automatically, and the last stat does not get a line under it. Adding the section from **Add section** inserts the three stats from the design, because the file includes a preset named “Nutrition highlights”.

The photo uses Shopify’s `image_url` and `image_tag`, so the theme serves a `srcset`, a `sizes` list, and lazy loading. If the image has no alt text, the heading is used so the photo is still described.

CSS is scoped to `#shopify-section-{{ section.id }}`. Two copies of this section on one page do not restyle each other, and they do not restyle the rest of the theme.

On a phone, the writing stays above the photo so the heading is read first. The left/right choice applies from 768px upward.

The section loads Inter Tight so a stock theme still matches the file. If the store already has a brand font, move that font link into `theme.liquid` (so it is not repeated) and change the `--font-family` line so the theme font comes first.

## Design decisions and assumptions

- Values that are visible in the Figma file are used as-is: background `#F8F8F8`, accent `#EE6F4B`, title `#161723`, body `#424153`, line `#E3E3E8`, Inter Tight, 6px button corners, 10px photo corners, 80px vertical padding, 30px column gap.
- The content column is 1170px, not a guessed 1140. That is 570 + 30 + 570, which is how the frame is built.
- Button links are `#` because the Figma buttons have no URL.
- The accent colour fails contrast. It is unchanged, and a passing replacement is listed above.
- Payment marks are the five in the file, in this order: PayPal, Visa, Mastercard, Apple Pay, Google Pay. They are not links.
- The favicon is a simple coral mark so the browser tab is not empty. It is not a logo from the file.

## How to run locally

The page is static. From the project folder:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`. You can also double-click `index.html`. A small server is the better check, because it matches the way GitHub Pages will serve the files.

You need a network connection the first time, so Inter Tight can download. If it cannot, the page falls back to Inter and then the system sans-serif.

## How to install the Liquid section in a theme

1. In Shopify admin, go to **Online Store → Themes**.
2. On the theme you want, click **⋯ → Edit code**. Do this on a duplicate theme first if the store is live.
3. Under **Sections**, click **Add a new section**.
4. Name it `nutrition-highlights`. Shopify saves it as `sections/nutrition-highlights.liquid`.
5. Replace the contents with the file in this repository: `shopify/sections/nutrition-highlights.liquid`.
6. Save.
7. Open **Customize** on that theme.
8. On the page where the section should appear, click **Add section** and choose **Nutrition highlights**.
9. Upload the dog photo (the file in this repo is `assets/dog-nutrition.webp`), set the button link, and adjust padding or image side if you want.

No extra CSS file is required. The section carries its own styles. To remove it, delete the section in the customizer, or delete the liquid file from the theme code.
