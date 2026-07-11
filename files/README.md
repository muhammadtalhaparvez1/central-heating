# The Docket — build brief

**For: Claude Code.**
**With: `docket.html`** — a complete, dependency-free reference UI.
**Goal:** a live, functional website for a UK central heating business, fast.

---

## How to use this file

1. Read the whole document before writing a line of code.
2. **Ask the owner every question in Part 1.** Ask them in one message, numbered, so he can answer in one reply. He is not a developer; do not ask him about endpoints or schemas.
3. Put his answers into `SITE` and `JOBS` (Part 4). Nowhere else.
4. Build what Part 6 says is missing.
5. Work the checklist in Part 9.
6. Set `dev: false`. Launch.

Everything the owner needs to supply is already a placeholder in the file, painted highlighter-yellow. **Nine of them.** They exist so the site cannot be launched with a made-up Gas Safe number.

---

# Part 1 — The intake

Ask all of it. Do not guess, do not fill gaps with plausible-sounding values, and do not proceed on partial answers where the answer is marked **blocking**.

### A. Identity and contact

| # | Question | Format needed | Lands in |
|---|---|---|---|
| 1 | Trading name? | As it should appear | `SITE.name` — header, footer ×2 |
| 2 | Phone number? | Display format, e.g. `020 8123 4567` | `SITE.phone` — 5 places. `tel:` link is derived by stripping spaces |
| 3 | WhatsApp number? | **International, no `+`, no leading `0`.** `07700 900123` → `447700900123` | `SITE.whatsapp` — 4 WhatsApp links |
| 4 | Is that WhatsApp Business or personal? | — | Affects away-messages for out of hours |
| 5 | Email address? | — | `SITE.email` — Section 06 |
| 6 | Opening hours? | e.g. `MON–SAT 08:00–18:00` | `SITE.hours` — masthead |
| 7 | Do you want a physical address shown? | — | Footer + `HVACBusiness` schema |

### B. Credentials — **blocking**

| # | Question | Format | Lands in |
|---|---|---|---|
| 8 | Gas Safe Register number? | 7 digits | `SITE.gasSafe` — **5 places**, including inside the rubber stamp |
| 9 | Is the registration currently live? | Yes/no | If no, the site does not launch. See Part 8.1 |
| 10 | Limited company or sole trader? | — | Footer legal line |
| 11 | If limited: company number, registered address, VAT number? | — | Footer. **Legally required** (Part 8.4) |

### C. Promises — each of these is a contract

| # | Question | Lands in |
|---|---|---|
| 12 | Is diagnosis genuinely free, or is there a call-out fee refunded against the work? | `SITE.callout` — hero meta, masthead strip, Section 02 terms |
| 13 | What is the workmanship guarantee period? | `SITE.guarantee` — hero meta, Section 02 |
| 14 | Do you actually offer same-day repairs? | Section 05, clause copy |
| 15 | Do you have a Google / Checkatrade rating yet? Average and review count? | `SITE.rating`. **If none, leave it blank.** See Part 1 note below |

### D. Prices — eleven figures, inclusive of VAT

Ask for the **starting** price of each. The upper bound is your estimate of the realistic ceiling; agree it with him.

| # | Job | `JOBS` key |
|---|---|---|
| 16 | Boiler repair | `repair` |
| 17 | Boiler service | `service` |
| 18 | Gas safety certificate (CP12) | `cert` |
| 19 | System bleed and rebalance | `bleed` |
| 20 | Leak trace and repair | `leak` |
| 21 | Expansion vessel replacement | `vessel` |
| 22 | Diverter valve repair | `diverter` |
| 23 | Power flush | `flush` |
| 24 | Heat exchanger descale | `descale` |
| 25 | Boiler installation | `install` |
| 26 | Full central heating system | `system` |

Also ask: **27.** Are you VAT registered? The footer states prices include VAT.

### E. Coverage

| # | Question | Lands in |
|---|---|---|
| 28 | Which postcodes and towns, exactly? | `SITE.areas` — Section 06, and `areaServed` in schema |
| 29 | Do you want a page per town for search? | Part 7.3. Usually no |

### F. Proof

| # | Question | Lands in |
|---|---|---|
| 30 | Can you send four photographs of your own jobs? | Section 03. Suggested: combi swap before, combi swap after, cylinder install, copper pipework |
| 31 | Do you have any real reviews? Which platform, and may I paste them verbatim? | Section 04 |

### G. Operations

| # | Question | Why |
|---|---|---|
| 32 | Where should enquiries go — an inbox, a spreadsheet, a phone notification? | Determines the form endpoint (Part 6.1) |
| 33 | Do you want customers to receive a confirmation email? | Part 6.1 |
| 34 | Do you want a real job reference number, or should the site not pretend to have one? | Part 5.5 |
| 35 | Do you have a domain and hosting yet? | Part 2 |
| 36 | Do you want analytics? | If yes, use a cookieless provider and skip the consent banner |

### H. Language

| # | Question |
|---|---|
| 37 | Is **"docket"** the right word for your customers? It reads Irish, Scottish, or Australian. In England most people say **job sheet**. The concept survives the rename: "Start a job sheet", "Send this job sheet". |

> **On question 15.** If there is no rating yet, the field stays as `—`, an em dash. That is a deliberate empty state, not a bug. An empty rating is honest. An invented one is a Google policy violation that earns a manual action. Do not write `4.9`.

---

# Part 2 — Stack

The prototype is one HTML file, 43 KB, no dependencies, no build step.

**Default: keep it static.** Cloudflare Pages, Netlify, or Vercel. The form goes to Formspree, Web3Forms, or a Cloudflare Worker. Total cost near zero.

**Use Next.js only if** the owner answered yes to question 29 and genuinely wants dozens of `{service} in {town}` pages.

Do not add React for one page. Do not add Tailwind to a finished stylesheet. Do not add an animation library for two animations. The surveyed competitor set ships multi-megabyte WordPress homepages; being fast is the advantage.

---

# Part 3 — The website, element by element

The concept: **the page is a carbon-copy job sheet.** The paper form a heating engineer fills in on a call-out. Graph paper, ballpoint indigo, red rubber stamp, highlighter. Nothing is rounded. Shadows are hard offsets, never blurred, because ink does not blur.

## 3.1 Document skeleton

```
<div class="masthead">        black strip: reference, Gas Safe no., promise, hours
<header>                      sticky. brand, nav, phone, CTA, burger
<div class="hero" id="top">   headline, buttons, four meta values, the stamp
<section id="triage">         01 — symptoms + the live docket panel
<section id="rates">          02 — the rate sheet
<section id="work">           03 — photographs
<section id="signoff">        04 — reviews, framed as a signed job sheet
<section id="terms">          05 — six numbered clauses
<section id="raise">          06 — contact details + enquiry form
<footer>                      tear-off strip: legal, nav, phone
<a class="fab">               floating WhatsApp, desktop only
<div class="bar">             fixed Call / WhatsApp / Docket, mobile only
```

Every section opens with the same block:

```html
<div class="sec-head">
  <span class="no">01</span><span class="of">of 06 · symptoms</span>
  <h2>What is it doing?</h2>
  <p>One paragraph. Max 58 characters per line.</p>
</div>
```

Section numbers `01`–`06` are legitimate here because a form's sections are numbered and the visitor walks a sequence. They were **removed from the nav bar**, where they meant nothing — a website is not read in order.

## 3.2 Masthead — `<div class="masthead">`

Four Courier items: job reference, `GAS SAFE REG {number}`, `NO CALL-OUT FEE`, opening hours. The third item hides under 700px to stop wrapping.

`NO CALL-OUT FEE` is a commercial promise. **Question 12.** If diagnosis is charged, this strip must say so instead.

## 3.3 Header — `<header>`

| Element | Tag | What it does |
|---|---|---|
| `.brand` | `<a href="#top">` | Business name in condensed black, plus a red-outlined `<em>Gas Safe</em>` chip |
| `nav#nav` | `<nav>` with 6 `<a>` | Scroll links. Under 1000px becomes a full-width dropdown |
| `.tel` | `<a href="tel:…">` | Courier. Hidden under 1000px — the mobile bar carries it |
| `.btn.btn-red` | `<a href="#triage">` | **Start a docket.** Red = the primary action of the whole page |
| `#burger` | `<button>` | Toggles the nav, toggles `aria-expanded`. Visible under 1000px only |

**The `<em>Gas Safe</em>` chip is decorative text.** It is not a link and it is not the official logo. Once registration is confirmed (question 9), replace it with the official Gas Safe Register badge and link it to the business's public entry on `gassaferegister.co.uk`, so a visitor verifies the licence in one click. **That link will convert better than anything else on the page.**

## 3.4 Hero — `<div class="hero" id="top">`

Headline: *The price goes on the docket first.* The word `first.` carries the first of exactly two highlighter animations.

Sub-line makes an explicit promise: *before you give us your name.* **Honour it. Never gate the estimate behind a form.**

Two buttons: red **Start a docket** → `#triage`; outline **Call {number}** → `tel:`.

Four meta values on a dashed rule, all four bound to `SITE`, **all four marked TODO**:

| Label | Value | Key | Note |
|---|---|---|---|
| Call-out | `£0` | `callout` | Question 12. A contract |
| Guarantee | `12 months` | `guarantee` | Question 13. A contract |
| Gas Safe | `0000000` | `gasSafe` | Question 8. Blocking |
| Rating | `—` | `rating` | Question 15. **The dash is deliberate** |

### The rubber stamp — `<svg id="stamp" role="img">`

The signature element. `viewBox="0 0 220 220"`. Thumps down 420 ms after load: scale 1.7 → 0.94 → 1, settling at `-8.5deg`. Text runs on two `<textPath>` rings. The Gas Safe number sits in the middle.

- It is **meaningful, not decorative**: it asserts "Gas Safe registered", the single most important claim on the site. It has `role="img"` and an `aria-label`.
- Under `prefers-reduced-motion: reduce` it appears instantly at final position. **Test this with the OS setting on.**

## 3.5 Section 01 — Symptoms — `<section id="triage">`

This replaces the fault-code lookup that some competitors have, because **most people do not have a code on the display. They have a cold house.**

Twelve checkboxes in a bordered two-column grid. Each is a real `<input type="checkbox">`, visually hidden but keyboard-focusable, wrapped in a `<label>` with a `<span class="box">` drawn in CSS. The tick is rotated borders in stamp red.

Non-urgent items show `Likely: {cause}` in grey. Urgent items show `Don't wait on this one` in red.

**Two symptoms are deliberately self-defeating. Do not remove them.**

- *Radiators cold at the top, warm at the bottom* → trapped air → "often a five-minute fix". This tells the visitor to do it themselves.
- Clause 06 says outright that plenty of firms sell power flushes that aren't needed.

That is the trust strategy of the whole site. The people who bleed their own radiator were never a paying job. The people who read that and believe it become customers for the jobs that matter.

**Verify every symptom → cause mapping against current manufacturer guidance before launch.** They are common published diagnostics, but they are stated plainly and the owner is accountable for them.

### The gas alert — `<div class="alert">`

3px stamp-red border, below the grid. Carries the National Gas Emergency Service number, **0800 111 999**, free, 24 hours, and the correct order of actions: don't touch switches, open windows, gas off at the meter, everyone out, call. Then call the business.

**Never move it into an accordion, a modal, a tooltip, or the footer. Never collapse it. Never shorten it.**

## 3.6 The docket panel — `<aside class="docket">`

The signature interaction. Sticky at `top: 88px`. Carbon-pink, 2px indigo border, 6px hard shadow. Three parts:

1. `.docket-top` — "Your docket" + the reference number
2. `.docket-body` — `aria-live="polite"`. Empty state, or one `.line` per ticked symptom: the symptom, then `{cause} → {job name}` beneath it, then `£{lo}+` right-aligned
3. `.docket-foot` — the range, the disclaimer, two buttons

### The arithmetic

```js
const jobs = [...new Set(picked.map(p => p.job))];   // dedupe by JOB, not symptom
const lo = jobs.reduce((a,k) => a + JOBS[k].lo, 0);
const hi = jobs.reduce((a,k) => a + JOBS[k].hi, 0);
```

Deduplication matters. "No hot water" and "no heating" both point at `diverter`; the customer pays for one diverter valve, not two. `money()` rounds to the nearest £5.

### The disclaimer — load-bearing

> Indicative range from typical jobs. Not a quote. Your fixed price is confirmed in writing before any work starts, and it doesn't move unless you ask for more work.

Do not shrink it, collapse it, or move it below the button.

## 3.7 Section 02 — Rates — `<table class="rates">`

A rate sheet, not a pricing-card grid. Three columns: job name, **what moves the price**, and the `from` figure.

The middle column is the point. Every competitor publishes a number with no explanation, so every visitor assumes the number is a lie. Explaining *why* a leak trace ranges £120–£420 — "where the leak is; under floors costs more than under the boiler" — is what makes the number believable.

Below it, three terms: no call-out fee, extra work, guarantee.

## 3.8 Section 03 — Work — `<div id="gallery">`

Four square hatched placeholder tiles. Replace with the owner's own photographs:

```html
<img src="/img/combi-swap-after.webp"
     alt="New combi boiler installed in an airing cupboard, copper pipework"
     width="800" height="800" loading="lazy" decoding="async">
```

WebP or AVIF, ~800×800, under 80 KB each. Explicit `width`/`height` to prevent layout shift. `alt` describes the work, not the marketing.

**Never scrape competitor images.** Manufacturer press shots are licensed material. The gallery captions are prompts telling the owner what to photograph.

`#m2` — "Every photo is one of our own jobs" — is the second and final highlighter, fired once by an `IntersectionObserver` at 60% visibility, which then disconnects.

## 3.9 Section 04 — Sign-off — `<figure>` × 3

Framed as a signature block: stars, quote, then a rule with the customer name left and the source right, like a signed job sheet.

Three placeholder cards ship reading *"Paste a real, verifiable review here — word for word, including the bits that aren't flattering."*

**Do not write reviews. Do not paraphrase reviews. Do not tidy reviews.** If there are none (question 31), delete the section. An absent reviews section is better than a fictional one.

If pulling live from Google: Places API, server-side, cached, rendered at build. Never expose a key client-side.

## 3.10 Section 05 — Terms — `<div id="clauses">`

Six numbered clauses, small print set large. `01`–`06` in stamp red.

**Not an accordion.** Everything is visible. Hiding answers behind a click implies you would rather not give them.

Clause 03 tells the visitor to check the engineer's Gas Safe ID card at `gassaferegister.co.uk` before letting anyone in, *"and that goes for us and for everyone else."* Keep that sentence. It costs nothing and it is the most persuasive thing on the page.

Wrap these in `FAQPage` JSON-LD (Part 7.2).

## 3.11 Section 06 — Raise a job — `<section id="raise">`

Dark indigo, grid background removed. Left: phone, WhatsApp, email, coverage, Gas Safe number. Right: the form.

| Field | `id` | Notes |
|---|---|---|
| Name | `f-name` | Required |
| Phone | `f-phone` | Required |
| Postcode | `f-post` | — |
| **Attached docket** | `f-docket` | **`readonly`.** Auto-filled by the triage |
| Anything else | `f-msg` | Free text |
| Send docket | `send` | `<button type="button">` |
| Confirmation | `sent` | `hidden` until success |

The **Attached docket** field is the practical payoff of the entire concept: the engineer arrives knowing which parts to load into the van. Say that to the owner if he questions it.

> **Known defect.** `#form` is a `<div>`, not a `<form>`. There is no native submit, no Enter-to-send, and screen readers get no form landmark. **Convert it to `<form>` with `novalidate`, keep the button as `type="submit"`, and handle `submit` in JS.** Do this before anything else.

## 3.12 Footer and mobile bar

Footer is a tear-off strip: dashed top border, Courier, three groups. The legal line carries company registration placeholders, the VAT statement, and the estimates disclaimer.

The bar is a fixed three-column strip under 1000px: **Call** (red), **WhatsApp** (green), **Docket**. `body` gets `padding-bottom: 56px` so it never covers content. The floating WhatsApp button is hidden here, so green never appears twice.

---

# Part 4 — The data objects

Everything the owner edits is in one place. He must be able to change his phone number without opening a second file.

## 4.1 `SITE`

```js
const SITE = {
  dev: true,                        // true = highlight every unfilled value
  name:      "Your Business Name",  // Q1
  phone:     "01234 567890",        // Q2   display format
  whatsapp:  "447000000000",        // Q3   international, no +, no leading 0
  email:     "hello@example.co.uk", // Q5
  hours:     "MON–SAT 08:00–18:00", // Q6
  gasSafe:   "0000000",             // Q8   BLOCKING
  rating:    "—",                   // Q15  leave as — if none
  callout:   "£0",                  // Q12  a contract
  guarantee: "12 months",           // Q13  a contract
  areas:     "TODO: postcodes",     // Q28
  gallery:   [ /* 4 captions */ ],  // Q30
  reviews:   [ /* 3 placeholders */ ] // Q31
};
```

Derived at runtime, never stored: `telHref`, `waHref`, `mailHref`.

**`dev: true` is a safety catch, not a debug flag.** While true, every unfilled value glows yellow. The site cannot be quietly launched with a fake registration number. It is the last thing you turn off, after every other checklist item.

## 4.2 `JOBS` — eleven entries

```js
repair: {
  name:  "Boiler repair",
  lo:    90,      // the "from" price. Questions 16–26
  hi:    320,     // the realistic ceiling. Agree it with the owner
  moves: "Which part failed, and whether we can fit it on the first visit."
}
```

`lo` appears in the rate sheet and on each docket line. `lo` and `hi` bound the estimate. `moves` is the sentence that makes the number credible.

Keys are referenced by `SYMPTOMS[].job`. **There is no fallback — an unknown key throws.**

## 4.3 `SYMPTOMS` — twelve entries

```js
{ s:     "No hot water, but the heating works",  // what the visitor reads
  cause: "Diverter valve stuck on heating",      // plain-English likely cause
  job:   "diverter",                             // key into JOBS
  urgent: true }                                 // optional; red sub-label
```

Display order is array order. Two columns, so keep the count even.

## 4.4 `CLAUSES`

`[question, answer]` tuples. Rendered into Section 05 and into `FAQPage` JSON-LD. The two must match exactly.

## 4.5 The templating system

There isn't a framework and there doesn't need to be one.

| Attribute | Effect |
|---|---|
| `data-bind="phone"` | Sets `textContent` from `SITE.phone` |
| `data-bind-href="telHref"` | Sets `href` from the derived link |
| `class="todo"` | Painted yellow while `dev: true` |
| `class="js-wa"` | Its `href` is rewritten by the docket engine |

Add an attribute, add a key to `SITE`, and it renders.

---

# Part 5 — Behaviour

## 5.1 The docket engine

One function, `update()`, fires on any checkbox `change` and writes to **six** things:

1. `#docketBody` — the line items
2. `#total` — the range
3. `#f-docket` — the hidden form field
4. `#waCount` — the badge on the floating button
5. `href` of all four `.js-wa` links
6. The empty state, when nothing is ticked

## 5.2 WhatsApp

Four entry points, all class `js-wa`, all kept in sync:

1. Floating button, bottom-right, **desktop only**, with a red count badge
2. Mobile action bar
3. "Send on WhatsApp" in the docket foot
4. "Open WhatsApp with your docket" in Section 06

It uses `https://wa.me/{number}?text={encoded}`. No SDK, no script tag, no tracking pixel. Opens the native app on a phone, WhatsApp Web or Desktop on a computer.

### The number format — question 3

`07700 900123` → `447700900123`. **Get this wrong and WhatsApp opens an "invalid number" screen rather than throwing an error you can catch.** Test on a real phone *and* a desktop browser.

### The pre-typed message

With two symptoms ticked:

```
Hello, I found you on your website.

DOCKET HTG-4821

What it's doing:
• No hot water, but the heating works
• Pressure keeps dropping on the gauge

Site suggested: Diverter valve repair, Leak trace & repair
Estimate shown: £300 – £840

My postcode is:
```

It ends on `My postcode is:` deliberately. The cursor lands there, the customer types the one thing the business needs, and sends. Two symptoms → 430-character URL. All twelve → under 1 KB.

When nothing is ticked, the message degrades to a polite generic opener. **Never leave the link with an empty `?text=`.**

## 5.3 Animation — exactly three things move

| What | Trigger | Reduced motion |
|---|---|---|
| The stamp | 420 ms after load, once | Renders instantly at final position |
| Highlighter `#m1` | 700 ms after load | Renders fully drawn |
| Highlighter `#m2` | `IntersectionObserver`, 60%, disconnects after | Renders fully drawn |

**Never add a third highlighter.** The whole media query is at the bottom of the stylesheet. Test it with the OS setting enabled.

## 5.4 Focus and keyboard

3px stamp-red focus ring, 3px offset, on every interactive element. The symptom checkboxes are real `<input type="checkbox">` — visually hidden, never `display:none`, always focusable. **Do not replace them with `<div role="checkbox">`.**

## 5.5 `HTG-4271` — question 34

```js
const no = 'HTG-' + String(Math.floor(Math.random()*8999)+1000);
```

`HTG` is an invented abbreviation for "heating". The digits are random, regenerated on every page load, and printed in three places: masthead, docket header, WhatsApp message.

**It is a prop, and props become liabilities.** It resets on refresh. It is not unique — two visitors can hold `HTG-4271` on the same afternoon. Worst of all it implies a job numbering system the business does not have, so when a customer rings quoting it, there is nothing to look it up in.

That matters because everything else here is built on not claiming things that aren't true. A reference number that references nothing is the same category of lie, only quieter.

**Three options. Pick one and tell the owner which:**

1. **Make it real.** Generate server-side on submission, persist it, email it to the customer. Correct if there is any volume.
2. **Make it honestly provisional.** Keep it client-side, label the field *"Reference issued on submission."* One line of copy. Nobody is misled.
3. **Delete it.** Remove the three `docketNo` bindings. The panel reads "Your docket."

**Default to option 2** if there is no backend yet.

---

# Part 6 — What you actually have to build

The prototype renders. It does not function. These are the gaps.

## 6.1 The enquiry form — the only thing standing between this and a working site

Currently `#send` validates name and phone, reveals `#sent`, and `console.log`s. **It sends nothing anywhere.**

Requirements:

1. Convert `#form` from `<div>` to `<form>` (see 3.11).
2. Server-side validation. Never trust the client.
3. Honeypot field, plus a timing check — a human takes more than two seconds.
4. Rate limit by IP.
5. Deliver to wherever the owner said in question 32.
6. Confirmation email to the customer if question 33 is yes.
7. Persist submissions somewhere the owner can read **without a developer**.
8. Include the `#f-docket` string verbatim. It is the reason the engineer brings the right part.

## 6.2 The failure state

The prototype has success and nothing else. Write the failure copy.

Errors do not apologise and are never vague. Say what went wrong and what to do instead: *"That didn't send. Call {number} — we'll answer."*

## 6.3 The privacy notice

The form collects name, phone, postcode, and free text. That is personal data under UK GDPR. **There is no privacy page. Build one**, linked from the footer and from beside the send button. It must say what is collected, why, how long it is kept, and how to have it deleted. It must mention that WhatsApp messages are processed by Meta.

## 6.4 Everything from Part 1

Nine placeholders, eleven prices, four photographs, three reviews.

---

# Part 7 — SEO, performance, accessibility

## 7.1 Metadata

Unique `<title>` and description. `og:title`, `og:description`, `og:image` (1200×630), `twitter:card`. Favicon set. `sitemap.xml`. `robots.txt`.

## 7.2 JSON-LD

Two schemas, minimum:

- **`HVACBusiness`** — `name`, `telephone`, `email`, `address`, `areaServed`, `openingHoursSpecification`, `url`, `image`.
- **`FAQPage`** — generated from `CLAUSES`, matching the visible text exactly.

Add `aggregateRating` **only** when the rating is real and sourced. A fabricated one is a policy violation and earns a manual action. Do not add `Review` schema for placeholder reviews. Do not add `Service` schema carrying placeholder prices.

## 7.3 Town pages — question 29

Only if he wants them. Each needs genuinely distinct copy. Twenty near-identical pages is a thin-content penalty, not a strategy. Fewer, better pages win.

## 7.4 Budget

Lighthouse ≥ 95 on all four categories, mobile. LCP < 2.0 s. CLS < 0.05. Total transfer under 150 KB including fonts and four images.

The prototype is 43 KB with zero JS dependencies. **Do not regress this.** Every library must justify itself against a competitor set whose average homepage is several megabytes.

Self-hosting the two fonts removes the last two external requests. Subset to Latin, preload the Archivo variable file, keep Courier Prime at 400/700 only.

## 7.5 Accessibility — WCAG 2.2 AA

- Everything reachable and operable by keyboard, with a visible focus ring.
- Real checkboxes, not divs.
- `aria-live="polite"` on the docket panel.
- The stamp has `role="img"` and a label. The alert triangle is `aria-hidden`; its meaning lives in the adjacent text.
- Check contrast if you change `--pencil` or `--ink-2`.
- `prefers-reduced-motion` honoured, and tested.

---

# Part 8 — Legal

## 8.1 Gas Safe Register

The engineer must be currently registered — question 9. Displaying the number and the official logo is permitted only while registration is live, and the Register sets the rules for logo use. Obtain their artwork and guidance; do not recreate the mark.

The number appears in **five** places, all bound to `SITE.gasSafe`: masthead, hero meta, inside the stamp, Section 06, footer. **Never hard-code it.**

Link the badge to the business's public entry so visitors can verify.

## 8.2 Prices

State that figures include VAT if the business is VAT registered — question 27. The estimate is not a quote, and the page says so three times. Keep all three.

## 8.3 Data protection

See 6.3. If you add analytics, use a cookieless provider and skip the consent banner. If you must use cookies, the banner defaults to declining non-essential.

## 8.4 Company details

If it is a limited company, the registered address, company number, and VAT number are a legal requirement on the website. Questions 10 and 11.

## 8.5 Claims

"No call-out fee", the guarantee period, "same-day repairs" are contractual. Questions 12, 13, 14. **Delete any that are not true rather than softening them.**

---

# Part 9 — Launch checklist

Every line, before `dev: false`.

- [ ] Name, phone, WhatsApp (international format), email, hours, coverage
- [ ] Gas Safe number, verified live on the Register
- [ ] Gas Safe chip replaced with official artwork, linked to the public entry
- [ ] Call-out fee and guarantee period confirmed as true
- [ ] All eleven prices real, inclusive of VAT
- [ ] Symptom → cause mappings checked against manufacturer guidance
- [ ] `#form` converted from `<div>` to `<form>`
- [ ] Form wired to a real endpoint: server validation, honeypot, rate limit
- [ ] Failure state written
- [ ] Privacy notice written and linked
- [ ] Company number, VAT number, registered address in the footer
- [ ] Four real photographs, optimised, `alt`, explicit dimensions
- [ ] Three real verbatim reviews — **or the section deleted**
- [ ] Rating real and sourced, or left as `—`
- [ ] Reference number decision made and implemented
- [ ] Gas emergency notice present, visible, unmodified
- [ ] `prefers-reduced-motion` tested with the OS setting on
- [ ] Full keyboard pass — every control reachable, focus always visible
- [ ] WhatsApp tested on a real phone **and** a desktop browser
- [ ] Lighthouse ≥ 95 mobile, all four categories
- [ ] JSON-LD validates, no fabricated ratings or reviews
- [ ] `dev: false`, and no yellow markers remain anywhere

---

# Part 10 — Do not

1. Invent a Gas Safe number, a review, a rating, a statistic, or a price.
2. Scrape competitor images or manufacturer press shots.
3. Use manufacturer logos. Text chips only.
4. Move, hide, collapse, or shorten the gas emergency notice.
5. Gate the estimate behind an email address.
6. Remove the two self-defeating symptoms, or Clause 06.
7. Add a fourth accent colour, or a third highlighter.
8. Round the corners.
9. Add a hero carousel, a gradient, or a stock photo of a smiling engineer with folded arms.
10. Add a chat widget that pretends to be a person, or a countdown timer that pretends to be real.

---

# Appendix — the colour tokens

| Token | Hex | Where |
|---|---|---|
| `--paper` | `#FFFFFF` | Background |
| `--grid` | `#E4EAF3` | 26px graph-paper grid. Never darken it |
| `--carbon` | `#F4EFF1` | The pink copy. Docket panel, sign-off section. Used exactly twice |
| `--ink` | `#16224A` | Ballpoint indigo. All text, all borders. The default |
| `--ink-2` | `#57648C` | Secondary prose |
| `--pencil` | `#8A93AC` | Labels, captions |
| `--rule` | `#C6CFDF` | Hairlines, dotted separators |
| `--stamp` | `#C4362A` | The stamp, checkbox ticks, clause numbers, alerts, focus rings |
| `--marker` | `#FFE45C` | Highlighter. Two on the page, plus the TODO markers |
| `--ok` | `#1B6B4A` | Form success only |
| `--wa` | `#25D366` | WhatsApp controls only. **Never a general accent** |

**Typography.** Two families, one request.

- **Archivo** (variable, `wdth 62..125`, `wght 400..900`). Headings: `900`, `font-stretch:78%`, uppercase. This is the voice.
- **Courier Prime** (400/700). Every *typed* value: prices, phone numbers, the Gas Safe number, labels, section numbers, docket lines, nav, mobile bar.

**The rule that makes the design work:** if it would have been typed onto a real form, it is Courier. If it is prose the business is saying to you, it is Archivo. Never mix.
