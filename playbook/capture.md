# Capturing promises

Goal: every tracked candidate's commitments are archived and logged before they can change or disappear.

## Before an election

1. List nominees from an official or reputable source and confirm the election year.
2. Find each campaign site and its issue or priorities pages. Add each page to `capture/targets.csv`
   (slug, url, office, person_id, priority, active_until about four weeks after the election).
3. Add debates, forums, and candidate questionnaires as sources. Prefer the candidate's own words
   (campaign site, questionnaire, debate transcript) over a reporter's summary.
4. For each commitment, add a ledger row (`ledger/README.md`). One row per distinct commitment;
   split bundles like a "pledge" with five parts into five rows.
5. Classify honestly. "Lower costs for families" with no mechanism is a position, not a promise.
6. Quotes: under 15 words, marked `pending` until matched against the archived text, then `yes`.
   If the words came from a reporter or a photo caption, leave `quote` empty and paraphrase.
7. For candidates with a legislative record, fill `prior_record` with the relevant votes or "To check".

## After an election

- Winners: change `Pre-office` to `In progress` on the day they take office.
- Losers: change to `Moot`. Keep the rows.
- New commitments made in office (inaugural address, State of the State, budget message) get new rows dated when said.
