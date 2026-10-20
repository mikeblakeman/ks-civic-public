# Social posts

Posts are another place officials, candidates, and their staff make commitments. This playbook covers which
accounts count, what is pulled automatically, how to read posts, and how a post becomes a ledger row.

## What is covered

| Platform | How | Notes |
| --- | --- | --- |
| Bluesky | Automatic, daily, full history | Free public API. Text stays private in `archive/social/`. |
| X (Twitter) | By hand | X charges for reads and blocks automated readers. Mike shares a link and a screenshot. |
| Facebook, Instagram | By hand | Meta has no public API for this. Same screenshot process as X. |
| Official office sites | Automatic, daily archive | Press releases usually match what office staff post on X. |

Not used: paid X access (declined), logged-in scraping (against X's terms and puts Mike's account at risk), and
Wayback Machine copies (Claude's tools cannot fetch them). If Mike opens a Wayback copy himself and shares a
screenshot, treat it like any other screenshot and cite the Wayback link.

## Accounts

- `registry/accounts.csv` is the only list of accounts we trust. Every row needs the https page that links the
  account (`source_url`).
- `status`:
  - `verified`: the person's own site or an official government page links it.
  - `probable`: name and bio clearly match, but nothing the person controls links it. Pulled, but say "probable" when citing.
  - `candidate`: found by discovery, not reviewed. Never cite or pull.
  - `not_found`: linked somewhere, but the account does not exist or is gone.
  - `retired`: no longer used (for example, a campaign account after the race).
- `account_type` decides attribution:
  - `campaign`: "Holscher's campaign account posted ...". Campaign staff may write these.
  - `official_office`: run by government staff. "The Attorney General's office posted ...", never "Kobach posted ...".
    Ledger rows from these use `made_as = official`.
  - `personal`: the person's own account. "Wikle posted ...".
- Every Monday, discovery writes `data/social/account_candidates.csv`: links found on archived pages, and
  same-name Bluesky accounts with a Kansas hint. Review them. Promote one only with a source that ties it to the
  person. A name match alone is never enough; common names collide.

## Reading posts

- Index (public mirror, no text): `data/social/index.csv`, and table `social_posts` in `civic.db`.
  Columns include `commitment_terms` (phrases like "I will", "day one", "as governor") and `missing_since`.
- A flag means "read this one". It is not a finding. Most flagged posts are not promises.
- To read text from a scheduled task or project chat, fetch up to 25 posts at once:
  `https://public.api.bsky.app/xrpc/app.bsky.feed.getPosts?uris=<uri>&uris=<uri>`
  (the `uri` column). In a session with the private relay, read `archive/social/bluesky/<person>__<handle>.jsonl`.
- Reposts of other people are skipped. Replies are kept (`is_reply = 1`); commitments often appear in replies.
  Quote posts are kept (`is_quote = 1`); read the quoted post before characterizing it.

Useful query:

```sql
SELECT person_id, handle, account_type, created_at, commitment_terms, url
FROM social_posts
WHERE commitment_terms <> '' AND created_at >= '2026-10-01'
ORDER BY created_at DESC;
```

## From post to ledger row

1. Apply the same test as any statement: promise (specific, attributable, forward-looking), position, or value
   statement (not tracked).
2. Fill the row as usual, plus:
   - `source_type = social_post`; `source_url` = the post's own link; `source_title` = "Bluesky post by @handle" or
     "X post by @handle (campaign account)".
   - `made_on` = the post date. `made_as` follows `account_type` (see above).
   - Quote under 15 words, copied exactly.
3. Verification:
   - Bluesky: `quote_verified = pending`, then `python tools/verify_quotes.py --apply` matches it against the stored post.
   - X, Facebook, Instagram: Mike shares the link and a screenshot. Save the screenshot to
     `archive/evidence/social/<person_id>/<YYYY-MM-DD>_<platform>_<post id>.png` (private, never published),
     read the quote from it, set `quote_verified = screenshot`, and name the file in `notes`.
     A link with no screenshot stays `pending`; ask Mike for the screenshot.
4. If a post is also in a press release, news story, or debate, cite the most durable source and mention the post in notes.

## Deleted or changed posts

- `missing_since` is set when a post disappears from a complete listing. It means deleted, or the account went
  private or was suspended. Report only that: "no longer available as of <date>". Do not guess why.
- If an edited post's text changes, the earlier wording is kept as `earlier_text` in the private store.
- A ledger row stays when its post disappears. Add a dated note; the stored text or screenshot is the evidence.

## Neutrality

- Same rules for every account and party. Report what was posted, when, and by which account.
- Do not count posts, likes, or followers as a measure of anything unless Mike asks, and then say what it does
  and does not show.
- Tone, insults, and memes are not promises. Track them only if Mike asks, and then as positions or quotes with
  context, never as characterizations.
