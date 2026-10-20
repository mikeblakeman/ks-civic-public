# Promise ledger

One row per promise or position, for any official or candidate being tracked.
`tools/check_ledger.py` enforces these rules in CI.

## Kinds

| Kind | Meaning | Scored? |
| --- | --- | --- |
| promise | Specific, attributable, forward-looking commitment to an action or outcome | Yes |
| position | A stance without a specific commitment | No; tracked against votes, status is `Not scored` |

Value statements ("I love Kansas") are not logged.

## Status scale

Every promise gets two statuses. Effort is what the person did. Outcome is what the body, Legislature, or state delivered.
A governor who proposes a constitutional amendment that fails in the House kept faith on effort; the outcome is still Broken.

| Status | Effort (the person) | Outcome (the body or state) |
| --- | --- | --- |
| Pre-office | Made as a candidate; nothing to score until they hold the office | Same |
| Moot | Candidate lost; kept for the record, never scored | Same |
| Kept | Acted and voted consistently every time it came up | Adopted or achieved |
| Partial | Mixed record, or acted on a narrower version | A compromise version adopted |
| In progress | Not yet up for action, or action underway | Underway with a dated next step |
| Stalled | No action in 12+ months despite opportunity | No movement in 12+ months |
| Broken | Acted or voted against the commitment | Rejected or reversed |
| Unverifiable | No public record could show it either way | Same |
| Not scored | Used only for positions | Same |

Unresolved disagreements default to In progress or Unverifiable, never Kept or Broken.
A status changes only on a primary record (roll call, bill action, signed order, filing, budget line).

## Fields

| Field | Notes |
| --- | --- |
| promise_id | `<OFFICE><YY>-<PERSON>-<NN>`, for example `GOV26-HOL-03` |
| office, person_id, person, party | person_id matches `registry/officials.csv` |
| made_as | nominee, incumbent, or official |
| quote | Exact words, under 15. Empty when no direct quote exists |
| paraphrase | Neutral, plain language |
| specificity | 1 vague, 2 directional, 3 measurable |
| measurable_test | What record would show it was done |
| mechanism | Constitutional amendment, legislation, budget, executive action, litigation, office action |
| authority | sole (the office can do it alone), shared (needs others' votes), advocacy (can only push) |
| source_type, source_url, source_title | campaign_site, debate, questionnaire, news_report, official_record, social_post (the post's own link; see playbook/social.md) |
| made_on, captured_on | When it was said (if known) and when it was captured |
| quote_verified | `pending` until the quote is matched against the archived copy, then `yes`; `screenshot` for a social post checked against a saved screenshot (notes must name the file under `archive/evidence/`); `n/a` when there is no quote |
| prior_record | For candidates with a voting record: consistent, inconsistent, or to check, with the vote |
| notes | Caveats, conflicting figures, definitions still needed |
