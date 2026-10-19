# Statewide officials and the Legislature

## Data

- `data/kslegis/<biennium>/legislators.json`: every member for that biennium.
- `data/kslegis/<biennium>/voting_record_<person_id>.json`: votes for people listed in `config/kslegis.json`.
- `data/kslegis/<biennium>/governor_actions.json`: bills signed, vetoed, overridden.
- Files are named `voting_record_<person_id>__<kpid>.json`. One person can have two kpids (for example
  `rep_holscher_cindy_1` and `sen_holscher_cindy_1` after moving from the House to the Senate).
- In `civic.db`: `votes` (one row per member per roll call: body, person_id, member_id, biennium, measure, motion,
  action, occurred_on, position, tallies) and `governor_actions` (biennium, measure, title, status, action_date).
  Raw payloads stay in `raw_json` for anything not normalized yet.
- Confirmed API fields: legislators have kpid, chamber (senate or house), district, party, status (Active or
  Inactive). Votes have bill_no, motion, stance (yea, nay, ...), tally, and journal_element with action_label and
  occurred. Governor actions have bill_no, status (signed, vetoed, veto-overridden, line-item-veto,
  partial-override), last_action_label, last_date. The full OpenAPI schema is saved at
  `data/kslegis/openapi_schema.yaml`.
- Check `data/kslegis/last_run.json` warnings before trusting counts. A warning like "got 100 of 1200" means the
  pull was cut short.

Example: how did tracked people vote on SB 98 (2025)?

```sql
SELECT person_id, member_id, motion, action, occurred_on, position, tally_yea, tally_nay
FROM votes WHERE measure = 'SB98' ORDER BY occurred_on, person_id;
```

## Consistency check (candidates with a record)

1. For each promise, identify the bills that touched the same subject (search the voting record by bill number or title).
2. Report each relevant vote: bill, date, roll call, how they voted, what the bill did.
3. Label the promise consistent, inconsistent, or untested. Do not score it; this is context.
4. An acknowledged change of position (for example, Holscher on SB 98) is reported as inconsistent with the
   candidate's explanation alongside it.

## During session (January to May)

- Friday digest: new bills tied to ledger promises, committee and floor votes by tracked people, governor actions.
- Constitutional amendments need two-thirds of each chamber and then voter approval. Track each step.
