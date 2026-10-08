# Wedding Invitation Skin Generator: AI Spec (v1)

Give this file (plus `wedding-template.html`) to any AI model. The user then only has to describe the theme they want, for example "art deco midnight", "boho terracotta", "Korean minimal" or "Minangkabau gold".

---

## 0. How to use (for the human)

**Mode A (recommended): skin-only.** Attach `wedding-template.html` and this spec. Paste the prompt from §10A and add your theme description. The AI returns only a small CSS skin block, which you paste into the template. This is cheap, reliable and keeps all animations working.

**Mode B: full file from scratch.** For an AI that cannot take the template. Paste the prompt from §10B and add your theme description. The result is less predictable, so prefer Mode A.

---

## 1. Role and goal

You are a senior art director and front-end engineer. The task is to create a **new visual skin** for an existing single-file, GSAP-powered luxury wedding invitation. The skin must feel genuinely different, with its own palette, typography, shape language and ornament, while the structure, animation and JavaScript stay untouched.

A skin is **a block of CSS custom properties plus at most a few decorative overrides**. It is not a rewrite.

## 2. Inputs

1. `THEME`: the user's free-text description of the look and feel.
2. Optional `CONTENT`: names, date, venue, bank details. If given, update `CONFIG`. If not, leave it.
3. The template file `wedding-template.html`.

If `THEME` is vague, do not ask questions. Pick one strong, specific direction and state it in the Design Brief.

## 3. Output contract (Mode A)

Respond with these parts, in this order, and nothing else:

1. **Design Brief** (max 120 words): the skin name (a lowercase slug such as `deco`), the mood in one sentence, a palette table of `hex, role`, the font pairing, the shape language, the ornament and pattern idea, and the motion character.
2. **Font link**: the full updated Google Fonts `<link>` href to replace the existing one. Keep the existing families and add only new ones.
3. **Skin CSS block**: the `:root[data-skin="slug"]{...}` rule with every token from §5 filled, followed by at most 6 optional decorative override rules (see §7).
4. **CONFIG line**: the entry to add to `CONFIG.orn`, such as `deco:'◆'`.
5. **Picker button**: one `<button data-s="slug" aria-label="…" style="background:linear-gradient(135deg,BG,ACCENT)"></button>` line.
6. **Contrast report**: a short list of the ratios you checked (§8).

Do not output the whole HTML file and do not modify JavaScript.

---

## 4. Architecture you must NOT change

The JavaScript depends on these hooks. Never rename, remove or restructure them.

| Area | Hooks |
|---|---|
| Intro | `#intro`, `#open`, `.env`, `.card`, `.pocket`, `.flap`, `.seal` |
| Global UI | `#glow`, `#mus` (music), `#skins` (picker) |
| Hero | `.hero`, `.bg`, `.arc`, `.vig`, `h1[data-split]`, `.h-in`, `.scroll`, `.pt` (generated) |
| Couple | `.couple`, `.p1`, `.p2`, `.por[data-mask]` |
| Timeline (pinned) | `#tl`, `.st`, `.yr`, `#yr`, `.bar i` |
| Journey (horizontal) | `#hz`, `.trk`, `.pn` |
| Gallery (stacked) | `#gal`, `.cd` (5 cards) |
| Engagement | `.eng`, `#mask` |
| Details | `.det`, `.glass`, `#cdn`, `[data-u]`, `.map`, `.sch` |
| Quote | `.qt`, `#qt` |
| Family / RSVP / Gift / Wishes / FAQ | `.fam`, `.rsvp`, `#rsvp`, `#ok`, `.gift`, `#wl`, `details` |
| Closing / Footer | `.close`, `footer`, `#ft` |
| Generic | `[data-r]` (fade-up), `[data-split]` (word reveal), `[data-k]` (config text), `[data-mask]` |

Section order is fixed (21 sections: hero → couple → timeline → journey → gallery → engagement → details/countdown/dress code/venue/map/schedule → quote → family → RSVP → gift → wishes → FAQ → closing → footer).

Elements with `data-k="key"` are filled with `textContent` from `CONFIG`, so values must be **plain text, never markup**.

---

## 5. Token dictionary (fill ALL of them)

| Token | Controls | Rule |
|---|---|---|
| `--bg` | Page background | Main surface |
| `--ink` | Body and heading text | Contrast ≥ 7:1 on `--bg` |
| `--warm` | Alternate section background, card edges, text on gold buttons | Close to `--bg`, slightly different |
| `--champ` | Gallery section background, map, gradient end | A soft tint of the accent |
| `--gold` | Main accent: eyebrows, borders, italic emphasis, buttons | Name is historical; it can be any accent colour. Contrast ≥ 3:1 on `--bg` **and** on `--dark` |
| `--gold-hi` | Highlight for the shimmer text | Lighter or brighter than `--gold` |
| `--rose` | Secondary accent (journey numerals) | Different hue from `--gold` |
| `--card` | Glass-card fill | Semi-transparent rgba, or solid for flat skins |
| `--line` | Hairlines, dividers | The accent at 20–50% opacity |
| `--dark` / `--on-dark` | Engagement section and footer background and text | `--on-dark` on `--dark` ≥ 7:1 |
| `--serif` | Numerals, italic accents, quotes | Must have good digits and italics |
| `--sans` | Body font | Readable at 16px, weight 300–400 |
| `--display` | h1, h2, h3 | Usually `var(--serif)` or a distinct display face |
| `--hw` | Heading weight | Must exist in the font link |
| `--hc` | Heading text-transform | `none` or `uppercase` |
| `--ht` | Heading letter-spacing | −.05em to .1em (uppercase ⇒ positive) |
| `--em-style` | Style of `<em>` words | `italic` or `normal` |
| `--r` | Card corner radius | 0 to 24px |
| `--rb` | Button corner radius | 0 or 999px |
| `--por-r` | Portrait shape (CSS border-radius) | e.g. arch `999px 999px 0 0`, oval `50%`, square `0` |
| `--arc-r` | Hero decorative frame shape | `50%`, or an arch `50% 50% 0 0` |
| `--sh` | Glass-card shadow | A shadow, or `none` for flat skins |
| `--pat` / `--pat-o` | Fixed full-page pattern and its opacity | An inline SVG data-URI, or `none` and `0` |
| `--hero-base`, `--hero-ink`, `--hero-accent`, `--hero-line` | Hero background, text, accent and hairline colours | `--hero-ink` ≥ 7:1 on the hero gradient. **The hero can be dark in a light skin and vice versa.** |
| `--hg1`, `--hg2` | Two soft glow colours in the hero | rgba, 30–60% alpha |
| `--hb1`, `--hb2` | Hero base gradient, top-left to bottom-right | Solid colours |
| `--vig` | Hero vignette | A `radial-gradient(...)` or `none` |
| `--p1`, `--p2` | Bride and groom portrait placeholders | Any `background` value (gradient or image) |
| `--g1` … `--g5` | The five gallery cards | Five distinct gradients that sit together as a set |
| `--mask` | Engagement reveal panel | A gradient |
| `--env1`, `--env2`, `--env3` | Envelope body, shadow side and flap | Paper-like tones from the palette |
| `--seal` | Wax-seal fill | A radial-gradient; the seal text is always `#fbf8f2`, so keep the seal dark enough |

---

## 6. From description to tokens (process)

Follow these steps in order.

1. **Extract the mood** into 3 adjectives and 1 cultural or visual reference (a material, an era, a landscape, a textile).
2. **Choose the palette** (6–8 colours): background, ink, 1 main accent, 1 secondary accent, a dark, a tint, and the hero pair. Prefer a restrained palette. Use one hero accent, never a rainbow.
3. **Choose fonts**: one display font plus one body font, both from Google Fonts. Pair contrast with harmony (a high-contrast serif with a neutral sans, or a geometric sans with a humanist serif). Confirm the chosen weights exist.
4. **Choose the shape language**: sharp (`--r:0`), soft (`--r:22px`) or arched (`--por-r:999px 999px 0 0`). The shape should echo the theme (arches for temples, circles for romance, squares for modern).
5. **Choose the ornament**: a single Unicode divider character, plus optionally a subtle SVG pattern (opacity .08–.25).
6. **Fill the tokens** from §5, then run the checks in §8.

### Quick mapping (examples, not limits)

| Theme words | Palette direction | Type | Shape / pattern |
|---|---|---|---|
| Art deco, gatsby | Deep teal or black, brass gold | Poiret One or Cinzel, uppercase, wide tracking | Diamond lattice, sharp corners |
| Boho, rustic | Terracotta, sage, cream | Fraunces or Cormorant plus Jost | Oval portraits, soft cards, little pattern |
| Korean / Japanese minimal | Off-white, charcoal, one muted accent | Light sans display, generous spacing | Flat, `--sh:none`, hairlines |
| Beach, coastal | Sand, sea-glass, navy | Airy serif plus clean sans | Wave-line pattern, big radius |
| Dark romantic | Oxblood, black, antique gold | High-contrast serif, italic emphasis | Arches, vignette |
| Regional or cultural (batik, songket, ulos…) | The traditional palette of that textile | Dignified serif or inscriptional caps | A **new abstract geometric** pattern inspired by the textile |

**Cultural themes:** use abstract, respectful, geometric references. Do not reproduce sacred or religious symbols, national emblems or trademarked motifs, and do not use caricature.

---

## 7. Hard rules

### DO
- Define the skin only inside `:root[data-skin="slug"]{...}`.
- Use **inline SVG data-URIs** for patterns, with `#` written as `%23`.
- Use only **Google Fonts** (`fonts.googleapis.com`). Other font hosts are blocked on published pages.
- If `--bg` is dark, add `:root[data-skin="slug"] #glow{mix-blend-mode:screen}` (the default `multiply` is invisible on dark backgrounds).
- Keep the design restrained and let large type, spacing and one accent do the work.

### DON'T
- Don't change JavaScript, HTML structure, IDs, classes, section order or the GSAP timelines.
- Don't use remote images or any external asset except Google Fonts.
- Don't add more than **6 override rules**, and only for decoration (`box-shadow`, `outline`, `border`, `border-radius`, `background`, `color`). Never touch `position`, `display`, `width`, `height`, `margin`, `padding`, `transform`, `clip-path`, `opacity` or `overflow` on animated elements (`.st`, `.cd`, `.pn`, `.por`, `#mask`, `.hero *`, `[data-r]`, `.w-i`). GSAP animates these and your CSS would fight it.
- Don't use `background-clip:text` on `em`. Words are split into inline-block spans, so gradient text breaks. The `.shine` class already provides shimmer.
- Don't rely on colour alone to carry meaning, and don't use neon or heavy gradients.
- Don't put HTML in `CONFIG` values.
- Don't set `--hc:uppercase` together with a negative `--ht`.

---

## 8. Quality checklist (verify before answering)

1. Every token in §5 is defined.
2. Contrast: `--ink` on `--bg` ≥ 7:1; `--on-dark` on `--dark` ≥ 7:1; `--hero-ink` on the hero gradient ≥ 7:1; `--gold` on `--bg` and on `--dark` ≥ 3:1; text colour `--warm` on `--gold` (hover state of buttons) ≥ 3:1.
3. Every font weight referenced exists in the font link.
4. The five gallery gradients are distinct but share a family.
5. The skin differs from the existing skins (classic, java, modern, rosegold) in at least **four** of: palette, display font, shape, pattern, hero tone, casing.
6. Overrides are decorative only and number 6 or fewer.
7. The skin slug is lowercase, has no spaces, and is unique.

---

## 9. Worked example

**THEME:** "Art deco midnight, brass gold, teal"

**Design Brief:** skin `deco`. Glamorous 1920s evening. Palette: `#0f1a1a` background; `#efe6d2` ink; `#c9a24c` brass accent; `#7fb3a8` sea-glass secondary; `#08100f` deep dark. Fonts: Poiret One (display, uppercase, wide tracking) with Manrope. Sharp corners, diamond lattice pattern, ◆ ornament. Motion unchanged, with a stately feel from the wide tracking.

**Font link:** add `&family=Poiret+One` to the existing href.

**Skin CSS:**
```css
:root[data-skin="deco"]{--bg:#0f1a1a;--ink:#efe6d2;--warm:#142424;--champ:#1c3030;--gold:#c9a24c;--gold-hi:#f1d98a;--rose:#7fb3a8;--card:rgba(255,255,255,.05);--line:rgba(201,162,76,.45);
--dark:#08100f;--on-dark:#efe6d2;--display:'Poiret One',serif;--hw:400;--hc:uppercase;--ht:.08em;--em-style:normal;--r:0;--rb:0;--por-r:999px 999px 0 0;--arc-r:50% 50% 0 0;--sh:none;
--hero-base:#08100f;--hero-ink:#efe6d2;--hero-accent:#c9a24c;--hero-line:rgba(201,162,76,.5);--hg1:rgba(127,179,168,.4);--hg2:rgba(201,162,76,.45);--hb1:#142a2a;--hb2:#050b0b;--vig:radial-gradient(transparent 40%,rgba(0,0,0,.6));
--p1:linear-gradient(#1f4a46,#08100f);--p2:linear-gradient(#c9a24c,#3b2f12);
--g1:linear-gradient(160deg,#1f4a46,#08100f);--g2:linear-gradient(200deg,#c9a24c,#4a3a14);--g3:linear-gradient(140deg,#7fb3a8,#142a2a);--g4:linear-gradient(190deg,#f1d98a,#c9a24c);--g5:linear-gradient(150deg,#2b5e58,#0a1514);
--mask:linear-gradient(120deg,#1f4a46,#c9a24c);--env1:#d9c48e;--env2:#bfa56a;--env3:#ead9a6;--seal:radial-gradient(circle at 35% 30%,#2b6b64,#0f2c29);
--pat:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='40' height='40'%3E%3Cpath d='M20 0L40 20L20 40L0 20Z' fill='none' stroke='%23c9a24c'/%3E%3C/svg%3E");--pat-o:.12}
:root[data-skin="deco"] #glow{mix-blend-mode:screen}
:root[data-skin="deco"] .glass{outline:1px solid var(--line);outline-offset:-6px}
```

**CONFIG line:** `deco:'◆'`

**Picker button:** `<button data-s="deco" aria-label="Art deco" style="background:linear-gradient(135deg,#0f1a1a,#c9a24c)"></button>`

**Contrast report:** ink/bg ≈ 14:1, gold/bg ≈ 7:1, hero-ink/hero ≈ 14:1.

---

## 10. Copy-paste prompts

### 10A. Mode A (skin-only). Attach the template and this spec.

```
You are generating a new SKIN for the attached wedding-template.html.
Follow the attached "Wedding Invitation Skin Generator: AI Spec" exactly:
use §3 for the output format, §5 for the tokens, §7 for the hard rules and §8 as your checklist.

THEME: <describe the look and feel you want here>
CONTENT (optional): <bride, groom, date, venue, bank, or leave blank>

Return only the six parts listed in §3. Do not output the full HTML file.
```

### 10B. Mode B (full file, no template available)

```
Build ONE self-contained HTML wedding invitation (HTML+CSS+JS in a single file) for the theme below.

THEME: <describe the look and feel>
CONTENT: <bride, groom, date, venue, bank>

Requirements:
- CDN only: GSAP 3.12.5 + ScrollTrigger (cdnjs), optional Lenis (jsdelivr). Fonts from Google Fonts only. No remote images (use CSS gradients and inline SVG).
- Put ALL colours, fonts, radii and gradients in CSS custom properties on :root[data-skin="..."], using the token names in §5, so a new skin only needs a new token block.
- Put all text content in a window.CONFIG object and fill elements via data-k="key" using textContent.
- Sections, in order: envelope intro (opens on click, starts music) → hero → couple → pinned timeline → horizontal journey → stacked-card gallery → engagement → details (event, venue, dress code, countdown, map placeholder, schedule) → word-by-word quote → family → RSVP → gift → wishes → FAQ → closing → footer.
- Motion: ScrollTrigger scrub, pinned scenes, mask reveals, parallax, magnetic buttons, cursor glow. Honour prefers-reduced-motion. Animate only transform and opacity.
- Music: a toggle button and a generative Web Audio loop (no audio files).
- Mobile-first, responsive, safe-area insets, clean semantic HTML, commented sections.
- Follow the DO/DON'T rules in §7 of the spec. Output only the complete HTML file.
```

---

## 11. Tips for the human

- The more concrete the theme words, the better: a **material** ("brushed brass"), an **era** ("1920s"), a **place** ("Bali rice terrace") and a **feeling** ("hushed, candlelit") beat generic words like "elegant".
- If the result is close, ask for adjustments to the tokens only ("make the accent warmer", "swap the display font for a rounder one"). Don't ask for layout changes.
- Test in light and dark device settings, on a phone and on desktop, and tap through the envelope, the music button and each pinned section.
- Before sending the page to guests, set `picker:false` in `CONFIG` to hide the skin dots.
