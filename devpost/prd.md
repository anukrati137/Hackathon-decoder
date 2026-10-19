---
doc: prd
status: approved
---

# Hackathon Decoder — Product Requirements

A single-screen decoder for you this week: paste one hackathon page, optionally say who you are, get three honest answers (eligibility with a quote, deadline in IST, required vs optional submit). Source: `scope.md > The Core Loop`, `scope.md > The Unique Kernel`.

## The Core Journey

1. Open the app. One screen: a large paste box, a small optional “About me” box, one **Decode** button. No cards yet.
2. In the demo, paste the AWS hackathon page and type `college student, India`.
3. Click **Decode**. A short **Reading the page...** message appears while it works.
4. Three cards appear on the same screen, directly below the button: **Can I enter?**, **Your deadline**, **What you must submit**.
5. Success for the demo: one real page is handled correctly, and the eligibility card can show a blunt **No** (or Yes / Unsure) with the exact page quote underneath — for example a “Professionals/Post grads only” line. Source: `scope.md > What "Working" Looks Like`.
6. To try another page, clear the paste box and paste again. Nothing is saved.

## Screens and Layout

**One screen. No other pages.** Source: `scope.md > The POC Boundary`.

Top to bottom:

1. Large box labelled **Paste the hackathon page here**
2. Small optional box labelled **About me** (example: `college student, India, solo`)
3. One button: **Decode**
4. After a successful decode: three cards stacked below the button, in this order: Can I enter? → Your deadline → What you must submit

If the paste is empty or does not look like a hackathon page: show this message instead of the cards: **This doesn’t look like a hackathon page. Paste the full page text, including the rules and deadline.**

## Look and Feel

Clear, blunt **verdict stamp** on a **clean paper-like page**. For a laptop screen recording. Don’t spend time on extra styling.

- Off-white background, dark text
- One simple sans-serif font everywhere
- **Monospace only for quoted lines** from the page, so they look like evidence
- **Can I enter?** card: large bold stamp-style word — **green Yes**, **red No**, **amber Unsure**. Quote sits directly under it in a **plain bordered box**
- **Deadline** card: **big numbers** for the date and days left
- Checklist: simple checkboxes; **Required** group first; **Optional** in a **lighter shade**
- Minimal, uncluttered, generous spacing
- **No gradients.** No animations except the **Reading the page...** message

## Features and Behavior

### Paste and About me

The paste box takes the text of one hackathon page. About me is a **single optional line** (example: `college student, India, solo`). It is used only for this Decode and **never saved**. Source: `scope.md > The Core Loop`.

### Decode

Clicking **Decode** reads the pasted page (and about me if present) and then shows results on the same screen. While working, show **Reading the page...**.

### Can I enter?

Shows a large **Yes**, **No**, or **Unsure**, always with the **exact quote** from the page that decided it. Never guess. If About me is empty, or the page does not say who can enter, show **Unsure** and list who the page says can enter. Source: `scope.md > The Unique Kernel`.

Checkable:

- [ ] Stamp is Yes, No, or Unsure, with a quoted line from the paste underneath (or Unsure plus who the page says can enter)
- [ ] Demo can show **No** with a line such as professionals/post-grads only, when that is what the pasted AWS page says relative to `college student, India`

### Your deadline

If the page gives a **clear date and timezone**, convert to **IST** and show **days left** (example: `Oct 29, 1:30 AM IST, 15 days left`).

If there is **no deadline** on the page: the card says **No deadline found on this page** and nothing else.

If there is a date but it is **too messy or missing a timezone** to convert safely: **Couldn’t convert this reliably** plus the **exact date text quoted from the page**. Never invent a date or assume a timezone.

This card failing must **not** hide the other two cards.

Checkable:

- [ ] Clear date + timezone → IST + days left
- [ ] No deadline → exactly **No deadline found on this page**
- [ ] Messy/no timezone → **Couldn’t convert this reliably** + quoted date text

### What you must submit

A checklist with **Required** items first and **Optional** items in a separate, lighter group. Simple checkboxes (visual, for reading — not a saved to-do).

Checkable:

- [ ] Required group appears before Optional
- [ ] Required vs optional are visually separate (optional lighter)

### Retry

Clear the paste box and paste another page. Previous cards are replaced by the new decode (or by the friendly message). Nothing persists.

## States and Boundaries

- **First open** — paste box, about me, Decode button; no cards.
- **Working** — **Reading the page...**
- **Success** — three cards below the button; one missing piece does not block the other two.
- **Unusable paste** — empty text, or text that does not look like a hackathon page: **This doesn’t look like a hackathon page. Paste the full page text, including the rules and deadline.** No cards.
- **Nothing saved** — closing or refreshing forgets paste, about me, and results. No accounts. Source: `scope.md > Explicitly Cut`.

## Product Decisions

- **Single screen, Decode button, three cards below** — simple enough for an afternoon demo.
- **AWS page + `college student, India` as the demo path** — a real page from this week.
- **Stamp colors (green / red / amber) and quotes in a bordered monospace box** — verdict + evidence, not a dashboard.
- **Deadline failure is honest and local** — never invent dates; other cards still show.
- **If build time runs short, drop IST conversion first** — keep eligibility + must-submit. Source: `scope.md > Later`.

## What We're Building

The single-screen loop above: paste, optional about me, Decode, loading message, three cards with the behaviors and looks described, plus the unusable-paste message. Smallest working version that handles **one real hackathon page** correctly.

## Deferred From the POC

- Saving paste, about me, or results — one afternoon, no login. Source: `scope.md > Explicitly Cut`.
- IST conversion — **only if time runs short**; then keep eligibility + required submit. Source: `scope.md > Later`.

## Possible Later Enhancements

Comparing several hackathons; calendar/reminders; remembering about me. Not this proof.

## Non-Goals

- Accounts or profiles — out of scope.
- Calendars and reminders — not needed to prove the decoder.
- Comparing several hackathons — one page in.
- Study/revision features — different project.
- Extra pages, extra styling, gradients, extra animations — don’t spend the afternoon there.

## Open Questions

None that block spec. How the page is *read* (tools and technical approach) belongs in `4-spec`.
