# Statewide officials and the Legislature

## Data

- `data/kslegis/<biennium>/legislators.json`: every member for that biennium.
- `data/kslegis/<biennium>/voting_record_<person_id>.json`: votes for people listed in `config/kslegis.json`.
- `data/kslegis/<biennium>/governor_actions.json`: bills signed, vetoed, overridden.
- In `civic.db` these are rows of `raw_json` (source `kslegis`). Field names were not fully documented when this was
  written: inspect a few rows with `json_extract` first, then write the query. Record the field names you confirm
  in this playbook so the next run starts from them.

## Consistency check (candidates with a record)

1. For each promise, identify the bills that touched the same subject (search the voting record by bill number or title).
2. Report each relevant vote: bill, date, roll call, how they voted, what the bill did.
3. Label the promise consistent, inconsistent, or untested. Do not score it; this is context.
4. An acknowledged change of position (for example, Holscher on SB 98) is reported as inconsistent with the
   candidate's explanation alongside it.

## During session (January to May)

- Friday digest: new bills tied to ledger promises, committee and floor votes by tracked people, governor actions.
- Constitutional amendments need two-thirds of each chamber and then voter approval. Track each step.
