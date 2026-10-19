---
doc: checklist
status: approved
---

# Build Checklist

Build mode: learn

## Slices

- [ ] **1. Decode eligibility with page evidence**
  Becomes usable: A local Streamlit app accepts one hackathon page and optional About me text, then shows a Yes, No, or Unsure verdict with an exact supporting quote. Unusable input, missing keys, and API failures do not produce guessed results.
  Why now: Bootstrap is part of the first working behavior, and this proves the unique kernel while verifying the unfamiliar Gemini API and model choice early.
  PRD ref: `prd.md > The Core Journey` (steps 1-5), `prd.md > Paste and About me`, `prd.md > Decode`, `prd.md > Can I enter?`, `prd.md > States and Boundaries`
  Spec ref: `spec.md > How This Works, In Plain Language`, `spec.md > Stack`, `spec.md > Where It Runs and How Someone Tries It`, `spec.md > Components > Streamlit page (app.py UI)`, `spec.md > Components > Paste checker`, `spec.md > Components > Gemini extractor`, `spec.md > Components > Config`, `spec.md > File Structure`, `spec.md > External Services and Dependencies`, `spec.md > Important Failure Modes`
  Build: Create the app and README, define the required packages including `tzdata`, add the secret-free `.env.example`, use the documentation-verified `gemini-3.8-flash` model setting, and implement paste validation plus Gemini-backed eligibility extraction and evidence display. Keep missing-key and API-error paths fail-closed.
  Verify (mechanical): Run `python -m compileall app.py` and `streamlit run app.py`. With a configured key, decode a valid page and confirm a verdict plus quote; verify unusable input shows the exact PRD message without calling Gemini, and missing-key/API failures show no success cards.
  Learner check: Open the local app, paste the AWS hackathon page (or another real page), enter `college student, India`, and Decode. Check whether the verdict and quoted evidence match what the page says.
  Commit: `Build eligibility evidence decoder`

- [ ] **2. Decode deadline and submission requirements**
  Becomes usable: The same screen returns all three cards: eligibility, deadline in IST with days left or the honest quoted fallback, and Required/Optional submission checklists in the right order.
  Why now: This extends the proven extraction flow while keeping date conversion in Python and making unsupported timezone labels fail honestly.
  PRD ref: `prd.md > Your deadline`, `prd.md > What you must submit`, `prd.md > Retry`, `prd.md > Look and Feel`, `prd.md > States and Boundaries`
  Spec ref: `spec.md > Look and Feel`, `spec.md > Components > Date maths (Python only)`, `spec.md > Data Model`, `spec.md > Important Failure Modes`
  Build: Add a small explicit mapping for supported timezone labels such as `EDT` and `GMT+5:30`; convert only unambiguous date/timezone pairs to IST and calculate days left. Show the exact quoted date text with “Couldn’t convert this reliably” for missing, messy, or unmappable timezone data. Render Required before lighter Optional items and ensure a deadline issue does not hide the other cards.
  Verify (mechanical): Run `python -m compileall app.py` and exercise the running app with clear supported timezone labels, no deadline, and an unmappable timezone. Confirm IST/days-left or exact fallback text, both submission groups in order, and that other cards remain visible when deadline conversion fails.
  Learner check: Decode a real page, inspect all three cards, then replace it with another page or unusable text. Confirm the new result replaces the old one and nothing is remembered after refresh.
  Commit: `Complete deadline and submission decoding`

## Hands-on Checkpoints

- [ ] Early usable behavior explored — after slice 1, inspect the eligibility verdict and its quoted evidence; feedback can shape the remaining decode behavior.
- [ ] Final kick-the-tires exploration and feedback completed

## Final Review

- [ ] Final review complete — feedback resolved and learner confirms ready to ship

## Code Tour and App Map

- [ ] Learning activity complete — guided route, focused alternative, prior practice connected, or brief recap
- [ ] Optional edit and transfer reflection addressed — offered/declined/already covered/not applicable as appropriate
- [ ] `devpost/app-map.html` generated from finished code, checked, and shown, including a project-grounded practice to reuse

Activity and evidence: Not started.
Route and stops: Not started.
Edit outcome: Not started.
Reflection: Not started.
Activity mode: Not started.

## Revisions