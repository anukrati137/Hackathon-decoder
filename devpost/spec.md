---
doc: spec
status: approved
---

# Hackathon Decoder — Technical Spec

## How This Works, In Plain Language

You run **one Python file** on your laptop. **Streamlit** (a library that turns Python into a simple local webpage) opens that page in your browser so you can record it. You paste a hackathon page, optionally type About me, and click **Decode**.

Python then does three jobs, in order:

1. **Check the paste** — if it’s empty or doesn’t look like a hackathon page, show the PRD message. No AI call.
2. **Ask Gemini** (Google’s AI) **only** to pull three things from the paste: eligibility rules **with exact quotes**, the **raw deadline text including timezone**, and submission items. Gemini must not convert dates or decide IST.
3. **Your code** converts a clear date+timezone to **IST** and **days left**, using a small mapping for labels such as `EDT` and `GMT+5:30`. If the date or timezone label is missing, messy, or unmappable, show the PRD deadline message with the exact quote. Never invent a date or assume a timezone.

The **API key** stays in a local `.env` file on your machine. Streamlit’s page never sees it. Nothing is saved: close the tab or refresh and the paste is gone. No database, no website online.

This shape is smaller than a Java web app or a separate front-end: one language you already touch in Colab, one file to explain.

## The Core Journey Through the System

PRD ref: `prd.md > The Core Journey`.

1. You run `streamlit run app.py` → Streamlit starts a tiny program on your laptop and opens a browser tab.
2. First screen: paste box, About me, Decode. No cards. PRD ref: `prd.md > Screens and Layout`.
3. Demo: paste AWS hackathon text, type `college student, India`, click Decode.
4. If paste is unusable → show **This doesn’t look like a hackathon page. Paste the full page text, including the rules and deadline.** Stop. No Gemini.
5. If `GEMINI_API_KEY` is missing → clear error. Do not call Gemini. Do not guess cards.
6. Show **Reading the page...** → Python sends paste + about me to Gemini → Gemini returns JSON (quotes, raw deadline, submit items).
7. If the API fails → clear error. Do not guess. Previous cards (if any) should not look like a successful decode.
8. Python builds the three cards: eligibility stamp from Gemini’s yes/no/unsure + quote; deadline from **Python IST maths** (or the honest fallback); checklist Required then Optional. PRD ref: `prd.md > Can I enter?`, `Your deadline`, `What you must submit`.
9. Clear the box and paste again. Refresh/close forgets everything.

```
You (browser tab)
    → Streamlit (Python on laptop)
        → Gemini API (extract three things only)
        → Python zoneinfo (IST + days left)
    → three cards or one error/message
```

## Stack

| Piece | Choice | Why / tradeoff |
|---|---|---|
| Language | Python 3.10+ | Learner knows Python from Colab; `zoneinfo` is built in (no extra date library). Need a local Python install, not Colab, for Streamlit. |
| UI | Streamlit | Learner asked for a small local page from one Python file, easy to record. Tradeoff: Streamlit’s own header/chrome; we’ll style the paper/stamp look as far as Streamlit allows (custom CSS), not a fully custom website. Docs: https://docs.streamlit.io |
| Secrets | `python-dotenv` + `.env` | Key never in the page. Docs: https://pypi.org/project/python-dotenv/ |
| Extraction | Gemini API **free tier**, model name in **one constant** in `app.py` (start with `gemini-2.5-flash` or current **3.8 Flash / 3.5 Flash-Lite** if the build verifies those IDs). Learner agreed. Docs: https://ai.google.dev/gemini-api/docs/pricing — https://ai.google.dev/gemini-api/docs/models |
| Dates | Python `zoneinfo` + `datetime`, with `tzdata` for Windows timezone data ([docs](https://docs.python.org/3/library/zoneinfo.html), [package](https://pypi.org/project/tzdata/)) | Learner: dates never invented by AI. |

**Verify early in the build:** exact Gemini model id string that works on the free tier today (Google’s docs currently point new projects at 3.8 Flash or 3.5 Flash-Lite). Put the working id in the one constant.

Official Python SDK: Google Gen AI (`google-genai`). Docs: https://ai.google.dev/gemini-api/docs/quickstart

## Where It Runs and How Someone Tries It

- **Runs:** local process + browser tab. **No deployment.**
- **Needs:** Python 3.10+, internet for Gemini during Decode, a Gemini API key in `.env`.
- **Demo recording:** laptop screen of the local Streamlit tab. Submission still needs a short video **and** a public GitHub repo.

**README must include:**

1. Copy `.env.example` to `.env`.
2. Get a free key from [Google AI Studio](https://aistudio.google.com/) (Gemini API). Paste it as `GEMINI_API_KEY=...` — never commit `.env`.
3. `pip install -r requirements.txt`
4. `streamlit run app.py`
5. Open the URL Streamlit prints (usually `http://localhost:8501`).

**Git:** `.gitignore` already ignores `.env` and `.env.*` and allows `.env.example`. Keep that **before the first commit**. Never commit a real key.

`.env.example`:

```
GEMINI_API_KEY=your-gemini-api-key-here
```

(Placeholder only. Model name lives in code, not in `.env`, so there’s one place to change it.)

## Look and Feel

Implements `prd.md > Look and Feel` and `scope.md > Inspiration & Identity`.

- Off-white page, dark text, generous spacing, minimal Streamlit chrome (hide extra menus if easy).
- One sans-serif for UI; **monospace only for quoted evidence**.
- Stamp: large bold **Yes** green, **No** red, **Unsure** amber; quote in a plain bordered box under it.
- Deadline: big numbers for date and days left.
- Checklist: Required first; Optional lighter.
- No gradients. No animation except **Reading the page...** (Streamlit spinner/status).
- Streamlit cannot match a custom site pixel-for-pixel; honor the paper/stamp mood, not extra styling time.

## Components

### Streamlit page (`app.py` UI)

Paste box, About me, Decode, loading, three cards or one message. PRD ref: `prd.md > Screens and Layout`, `Paste and About me`, `Decode`, `Retry`.

### Paste checker

If empty or not hackathon-like → exact PRD unusable-paste sentence. No API call. PRD ref: `prd.md > States and Boundaries` (Unusable paste).

### Gemini extractor

Sends paste + optional about me. Instructs the model to return only: eligibility yes/no/unsure + exact quotes (+ who the page says can enter when unsure); **raw deadline string as on the page** (or none); required vs optional submit items. Must quote from the paste, not invent lines. PRD ref: `prd.md > Can I enter?`, `What you must submit`. Kernel: `scope.md > The Unique Kernel`.

### Date maths (Python only)

If Gemini returns a clear date **and** timezone → interpret supported labels with a small explicit mapping (for example, `EDT` and `GMT+5:30`), then convert to IST and calculate days left. Do not guess unsupported or ambiguous labels. If no deadline → **No deadline found on this page**. If the date or timezone is messy, missing, or unmappable → **Couldn’t convert this reliably** + quoted raw text. One card failing must not hide the others. PRD ref: `prd.md > Your deadline`.

### Config

One `MODEL_NAME = "..."` constant. Load `GEMINI_API_KEY` from `.env`. If missing → clear error, never guess.

## Data Model

No database.

| Data | Where it lives | When it updates | After leave/refresh |
|---|---|---|---|
| Paste, About me | Streamlit widgets in memory | As you type | Gone |
| Gemini JSON | Memory for this Decode only | After a successful API reply | Gone |
| Cards | Drawn from that memory | After Python finishes | Gone |
| API key | `.env` on disk | You edit the file | Stays on your laptop; not in git |

## File Structure

```
hackathon-project/
├── app.py                 # All UI + paste check + Gemini call + IST maths
├── requirements.txt       # streamlit, google-genai, python-dotenv, tzdata
├── .env.example           # GEMINI_API_KEY placeholder only
├── .env                   # real key — gitignored, never committed
├── .gitignore             # already ignores .env; keep it
├── README.md              # get a key + run commands
└── devpost/               # planning docs (learner-profile stays gitignored)
```

## External Services and Dependencies

**Gemini Developer API (free tier)**  
- Docs: https://ai.google.dev/gemini-api/docs/quickstart  
- Pricing: https://ai.google.dev/gemini-api/docs/pricing  
- Models: https://ai.google.dev/gemini-api/docs/models  
- Auth: API key in header/SDK from `GEMINI_API_KEY`.  
- Call: generate content with JSON response (eligibility, quotes, raw_deadline, required[], optional[]).  
- Cost: free within free-tier limits; demo is a few clicks.  
- Needs internet at Decode time.  
- **Check at build:** working model id for free tier; record it in `MODEL_NAME`.

No other hosted services.

## Important Failure Modes

- **Missing key** → message like: “Add your Gemini API key to a local .env file (see README). The app will not guess.” No cards that look successful.
- **API error / timeout / rate limit** → “Couldn’t read the page (API error). Try again. Nothing was guessed.” Never fill Yes/No or dates from thin air.
- **Unusable paste** → exact PRD sentence; no Gemini.
- **Deadline unclear** → PRD deadline card copy; other cards still show.

## What Was Simplified and Why

- **One `app.py`** instead of a Java/front-end split — afternoon demo, easier to explain.
- **Streamlit** instead of a custom website — fastest local page; some layout limits accepted.
- **Gemini extract-only** instead of asking the model for IST — protects the kernel (no invented dates).
- **No database / no deploy** — PRD non-goals; video + GitHub are enough.
- **If time runs short:** drop IST conversion first; keep eligibility + must-submit. Source: `scope.md > Later`.

## Decisions and Open Issues

- **Python + Streamlit, one file, local only** — learner. Tradeoff: new library, not Java.
- **Gemini free, `.env` key, never in the page** — learner. Tradeoff: Google account + internet at demo time.
- **`.env` gitignored, `.env.example` placeholder, `MODEL_NAME` one constant, README for key + run, fail closed** — learner.
- **AI vs Python split** — learner; the useful unknown to **explain in the build:** Gemini returns raw deadline text; `zoneinfo` does IST. Evidence: a demo where messy dates show “Couldn’t convert this reliably” + quote, not a guessed IST time.

Open: exact free-tier model id string — verify in `5-build`, then set the constant. Does not block this spec.
