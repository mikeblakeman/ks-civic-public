# Org chart and person profiles

A private page shows the officials and candidates we track as an org chart. Clicking a person opens a standard
profile. The page reads only the files in `data/profiles/`, which `tools/build_profiles.py` writes from `civic.db`.

## What builds what

| File | Built from | Notes |
| --- | --- | --- |
| `config/org.json` | Edited by hand | Groups and seats, in display order. No people here. |
| `registry/officials.csv` | Edited by hand | Who holds or seeks each office. Seats find people here by office and district. |
| `data/profiles/org.json` | Builder | The chart: groups, seats, holders, races, short person cards |
| `data/profiles/index.json` | Builder | Every person, for name search |
| `data/profiles/people/<person_id>.json` | Builder | One profile per person in the registry, on the chart or not |
| `schema/profile.schema.json` | Edited by hand | The profile format (version 1.0.0). The builder refuses output that fails it. |

The relay workflow runs the builder after "Build civic.db" on every run, so the mirror always carries fresh
profiles. To build by hand:

```
python -m civic.db --root . --out civic.db
python tools/build_profiles.py --root . --db civic.db --out data/profiles
```

## Add a person

1. Add a row to `registry/officials.csv` with a source page and as-of date. Status is `holder`, `nominee`,
   `former`, or `holder; nominee`.
2. If the office and district match a seat in `config/org.json`, the person appears on that seat. If not, they
   still get a profile and show up in search.
3. For Legislature votes, add them to `config/kslegis.json`. Votes appear after the next Monday pull.
4. For social accounts, follow `social.md`.

## Add a seat or a group

1. Add the seat to a group in `config/org.json`: `id` (unique), `label`, `office`, and `district` (empty for a
   statewide office). The office and district must match the registry exactly (case does not matter).
2. Boards elected at large use one seat with `multi_member` set to the number of seats. Missing members show as
   "Not in the registry".
3. A new group needs `id`, `label`, `partisan` (party shows only when true), `contribution_limit` (from
   `money-trail.md`), and `seats`. Groups show in file order. Seats in a group sort by office and district.
4. `holder_note` explains an empty holder slot. `governor_actions_since` gives a seat's holders the bills
   signed and vetoed from that date. Update it when a new governor takes office.
5. No code changes are needed. Run the tests to confirm the config loads.

For all 165 legislators later: add one seat per district to the Senate and House groups, and the people to the
registry. Groups start collapsed when they have more than 12 seats, and profiles load one at a time.

## Republish the private page

1. Pull the latest relay, or the mirror, so `data/profiles/` is current.
2. In a Claude chat in the Political Accountability Research project, ask Claude to republish the org chart
   artifact from `site/org-chart.html` with `data/profiles/` as supporting files, to the same artifact link.
3. The page is private. Do not publish it to GitHub Pages or anywhere public.

## Rules the page keeps

- Same layout and order for every person and party. Party always shows as a text label. Cards also get a light
  tint when party is known: blue for Democratic, red for Republican, none for other parties or nonpartisan offices.
  The tint never replaces the label, and nothing sorts or filters by party.
- No scores, grades, rankings, or percent-kept figures. Counts come with their definitions. Pre-office, Moot,
  and Not scored are counted apart from the scored statuses.
- Every count links to its data file and shows an as-of date. Bills always show their biennium.
- Uncertainty is labeled: pending quotes, probable accounts, partial vote pulls, and stale data
  (older than `stale_after_days` in `config/org.json`).
- Nothing from `archive/blobs`, `archive/text`, `archive/social`, or `archive/evidence` reaches the profiles.
  The builder strips paths into those folders and refuses output that names them.
