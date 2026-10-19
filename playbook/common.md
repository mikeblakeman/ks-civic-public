# Common setup for scheduled and on-demand runs

1. Get the data (public, no credentials needed):
   `git clone -q --depth 1 https://github.com/mikeblakeman/ks-civic-public.git ~/civic` (or `git -C ~/civic pull -q`).
   Work from `~/civic`. Query `civic.db` with Python's sqlite3; the CSVs are the same data in plain form.
2. Freshness: read `status.json`. Note `published_at` and any failed jobs. If data is older than the question needs,
   say how old it is. Only the private relay can refresh it (daily archive 8:17 AM CDT; Legislature and Census Mondays).
3. Shell downloads from city and state sites are blocked here. For anything not in the mirror, use web search and
   fetch, and treat what you find as a lead until a primary record confirms it.
4. Follow the lawrence-civic-research skill: source hierarchy, neutrality, claim check, donor tiers.
5. Check the election year on every candidate source. Voter guides from 2022 still rank highly in search results.
6. Writing rules for anything Mike reads: plain language, Central time, short sentences, no em dashes.
   Paraphrase news in one line with a source link; never copy article text. Quotes under 15 words.
7. Do not push to any repository from a scheduled run. Ledger changes are proposed to Mike in chat and land in the
   private relay through a commit he approves.
