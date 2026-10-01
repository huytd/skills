# Writing style for explanations

Applies to anything a person will read outside the code: Jira comments, PR descriptions, bug write-ups, status updates, summaries for PMs. Write for a smart reader who doesn't know the codebase.

## Structure

Follow this order and skip any part that doesn't apply:

1. **Context:** what the situation is, in one or two plain sentences.
2. **Cause:** the exact mechanism, with one concrete example using real values.
3. **Scope:** when it happens and when it doesn't. Explain why it usually works if that's the surprising part.
4. **Impact:** what is actually wrong and what is fine (e.g. "the stored date is correct; only the display is off").
5. **Fix:** one or two sentences on the solution.
6. **Follow-up:** related problems that are out of scope, each on one line.

Put **Fix** and **Follow-up** under bold labels. Keep the rest as short paragraphs, not bullets.

## Rules

- Lead with plain language and introduce technical names afterward, in backticks, only when the reader needs them (e.g. "the calendar date (`authorizationDate`)").
- Name the exact mechanism. Never use vague words like "drift", "glitch", "round up" or "somehow".
- Give one concrete example with real numbers: "noon on Sept 23 in Tokyo is 9 PM on Sept 22 in Denver".
- Don't hide the root cause behind its symptom. If bad data triggered the bug, say so (e.g. "the merchant was wrongly matched to Tokyo").
- Separate verified facts from assumptions, and mark what hasn't been checked.
- Keep sentences short, use active voice, and leave out filler and apologies.
- Use bold labels, not headers, for anything under ~15 lines.

## Example

❌ BE uses Japan timezone, FE converts to client timezone, drift causes round up of ±15h, so dates shift ±1 day.

✅ This expense came from a receipt, so it has a date but no real time. The backend stores that date as noon in the merchant's time zone. The merchant was wrongly matched to Tokyo, which is 15 hours ahead of the user in Denver. The web app shows that timestamp in the user's local time, and noon on Sept 23 in Tokyo is 9 PM on Sept 22 in Denver, so the user picks the 23rd and sees the 22nd. The stored date is correct; only the display is off.

**Fix:** use the calendar date the backend already provides (`authorizationDate`) instead of converting the timestamp.

**Follow-up:** check why Amazon.com from a US receipt was matched to an in-person merchant in Tokyo.
