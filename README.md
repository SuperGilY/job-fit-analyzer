# job-fit-analyzer

A Claude skill and subagent that answers one question fast: should I apply to this job, and with which CV?

Paste a job description. Get an HTML report with a one-screen TLDR on top and the details collapsed below.

## What it returns

- Recommendation: send, send with fixes, or skip
- Score (1-10) from a fixed formula, and odds of reaching an interview as a range
- Fit table: every requirement matched to evidence in your CV, or marked as missing
- Real gaps vs wording gaps. A real gap is something you lack. A wording gap is something you have but the CV does not say in the JD's language
- ATS keywords: what is there, what is worded differently, what is true and missing, what you should not add
- Which CV version to send, and why it beats the runner-up
- Interview risks with short honest answers, and questions to ask the recruiter
- A numbered edit list to tailor the CV, and a tracker row for your pipeline

Batch mode takes up to 5 JDs separated by `=== JD ===` and returns a ranking table first.

## Design choices

- **Decisions, not essays.** One recommendation, never a list of options.
- **No invented experience.** Every claim traces to a CV file or to your profile. Keywords you cannot defend in an interview are marked "do not add".
- **Honest odds.** Scores come from a formula (must-have coverage). Odds are labeled as estimates, with a column in the tracker so you can calibrate them against real outcomes.
- **Your CV keeps its design.** The skill outputs an edit list instead of rewriting the CV.
- **Private data stays private.** Your facts live in `candidate-profile.md` and `cvs/`, both gitignored. The skill itself is generic.

## Setup

1. Copy `candidate-profile.example.md` to `candidate-profile.md` and fill it in: target roles, skip rules, CV versions, verified facts, known gaps.
2. Put your CV files in `cvs/` and list them in the profile.
3. Open this folder in Claude Code. Paste a job description and ask: "does this fit?" or call the `job-fit-analyzer` agent.

LinkedIn blocks automated access, so paste the JD text instead of a link.

## Layout

```
.claude/skills/job-fit-analyzer/SKILL.md   the method
.claude/agents/job-fit-analyzer.md         the subagent that runs it
candidate-profile.example.md               template for your private profile
```

## Limits

- Odds are a judgment call, not a model trained on outcomes.
- Most ATS systems rank for a human recruiter. The skill only flags a screening risk when a JD states a hard requirement.
