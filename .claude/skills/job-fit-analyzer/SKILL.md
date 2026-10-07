---
name: job-fit-analyzer
description: Use when the user pastes a job description and wants to know if it fits, what the real gaps are, and which CV version to send. Reads candidate-profile.md and the CV files, then returns an HTML report with a TLDR on top and details collapsed - fit table, real gaps vs wording gaps, ATS keywords, odds, and a CV tailoring brief. Also handles batches of JDs.
---

# Job Fit Analyzer

Take one job description (or a batch) and the candidate's CV versions. Return a decision, not an essay: apply or skip, which CV, what to fix before sending.

## Inputs

1. **Job description** as pasted text. LinkedIn blocks automated access, so never try LinkedIn links. A company careers page may work: try once, then ask for the text.
2. **candidate-profile.md** - read it first. It holds the candidate's facts, target roles, CV versions, skip rules, confirmed experience and known gaps. Everything below is generic and depends on it.
3. **CV files** listed in the profile. Read them from disk. Never rely on memory of what a CV says.

If candidate-profile.md is missing, say so and point to candidate-profile.example.md. Do not guess the candidate.

## Step 0 - Skip check

Apply the skip rules from the profile (roles that share a title with the target but have different content, for example GTM, partnerships, implementation). If the JD is built around one, stop: one line, quote the triggering phrase. If it is only a minor mention inside an otherwise fitting role, flag it and continue.

## Step 1 - Extract

- Title, company, seniority, location and office policy
- Must-haves vs nice-to-haves. If the JD does not separate them, treat the first half of requirements as must-haves
- Hard filters: degree, years, named tools, domain
- What the job really is, in one line

Noise filter: ignore mission, values, perks, EEO and sponsorship text. Read title, responsibilities and qualifications only. Mention office attendance or sponsorship in one line only if it matters.

## Step 2 - Fit table

One row per requirement: requirement, evidence in the CV, strength (strong / medium / weak / none). Evidence must be a concrete line or number from a CV file. No evidence means "none". Never stretch.

If CV versions show different numbers for the same claim, say so. Do not pick silently.

## Step 3 - Gaps, split in two (the core of the skill)

- **Real gaps:** the candidate does not have it and wording will not fix it. Anything with no evidence in the CVs or the profile.
- **Wording gaps:** the candidate has it, but the CV does not say it in the JD's language. For each one, give a one-line fix written as a CV line that can be pasted.

Use the profile's "confirmed experience" and "known gaps" sections so confirmed items are not marked as gaps and unconfirmed items are asked about, not added.

## Step 4 - ATS keywords

Take 8-12 keywords from the requirements. Check each against the recommended CV:

- in the CV
- there, but worded differently
- true and missing: add
- not true: do not add

Use the JD's exact wording when the keyword is true. Never add a keyword the candidate cannot defend in an interview. No stuffing, no hidden text. Be honest: most ATS systems rank for a human recruiter and few hard-reject. Call something a screening risk only when the JD states it as a hard requirement.

## Step 5 - Recommendation

Pick one: send / send with fixes / skip. Never an open list of options.

**Score 1-10.** For each must-have: strong = 1, medium = 0.5, weak or none = 0. Score = coverage x 10, rounded, then +1 or -1 for nice-to-haves and role-type fit. Show the count ("5 of 7 must-haves").

**Odds** (an estimate, not data), by score band:
- 8+: 40-50%
- 6-7: 20-35%
- 4-5: 8-15%
- 3 and below: up to 5%
- Cap at 15% if a hard requirement is missing (a required stack, years of hands-on development, a degree not marked "or equivalent", a domain the candidate has not worked in)
- Add about 10 points if the candidate already spoke with a recruiter

Also give: which CV and why it beats the runner-up (one sentence), the top 2 interview risks each with a short honest answer, 3 questions to ask the recruiter that expose what the role really is, and a tracker row.

## Step 6 - Tailoring brief

End with a numbered edit list the candidate can paste into another chat together with the CV file: skills line additions, opening line, bullets to move up, keywords to add. Rules inside the brief: no invented experience, keep the original file untouched, save as a copy named `<Version>_<Company>`. Do not rewrite the whole CV here. The source file carries the design, and a rewrite loses it.

## Batch mode

Several JDs separated by a line `=== JD ===`. Max 5. Open with one ranking table (role, company, score, odds, CV, action), best first. Then a full report per JD. Skipped roles get one table row and no report. No separator and it looks like several roles: ask once.

## Output

One HTML file, named `fit-<company>-<role>.html`. Not Word, not markdown. Use the language and direction set in the profile.

- **Layer 1, TLDR (always visible, fits one phone screen):** recommendation, score, odds, which CV, 2 reasons for, 2 against, one action before sending.
- **Layer 2, details (each section in a closed `<details>`):** what fits, strengths in the CV, fit table, ATS keywords, real gaps, wording gaps with fixes, interview risks, recruiter questions, tailoring brief in a code block, tracker row.
- Cells under 10 words. Max 3 bullets per list. No intro, no summary paragraph, no softening.

Tracker row (CSV): date, company, role, score, odds, CV sent, action, main gap, outcome (empty at first). Append to `pipeline.csv` if it exists.

## Voice

Follow the voice rules in the profile. Defaults: plain hyphen only, short direct sentences, no filler openers, no tricolons, no inflated verbs, honest over flattering. A weak fit gets called weak. When writing Hebrew, write it natively and keep professional terms in English. Reread each line once. If it sounds translated, rewrite it.

## Guardrails

- Never invent experience, numbers, tools or titles. Everything traces to a CV file or the profile.
- Never suggest changing an official title.
- Do not send or submit anything. Output a report only.
- If the JD is too short to judge, say what is missing and ask one question.
