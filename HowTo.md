# Odds Board — How to use and review

The live app is [Odds Board](https://augim13.github.io/screener/odds-board.html). This repository contains the browser UI (`odds-board.html`, `game-hub.html`, and `index.html`). The API and private secrets live in the separate Cloudflare `my-odds-worker` project. The Board is a screening and record-keeping tool; it does not place wagers.

## 1. Connect

1. Open the live Odds Board in a current browser. Enter the `my-odds-worker` base URL and the Worker `PASS` from your own Cloudflare configuration.
2. Select **Test connection (free)** or **Connect & open Board**. The connection test uses a sports-list endpoint rather than a paid odds scan.
3. Keep `PASS`, `SYNC_KEY`, provider API keys, and private bet records out of GitHub, screenshots, and messages. The page says `PASS` remains in the open page; API keys belong in Cloudflare secrets.

If the page reports an unauthorized Worker response, check the URL and `PASS`. If a provider scan fails, open **Settings → Connection & source diagnostics** and use **Check source connections** deliberately; optional checks may consume provider quota.

## 2. Find lines

1. In **Board → Source & market**, select a source, sport, and **Game lines** or **Player props**. The default source is The Odds API; choose **BettingPros** for its model-backed player-prop workflow. Switching tabs or sources does not itself make a scan request.
2. **Check today's games (free)** checks the schedule, not whether that source has a quote.
3. For BettingPros props, choose the sport, one prop market or all available markets, pages to scan, and optional platform filter. **Check available books** and **Browse market categories** help diagnose coverage. **Scan player props** makes the actual request. If event IDs or dates remain unverified, use **Refresh matchups** before treating a line as actionable.
4. The Odds API scans may use credits; the Board estimates the prop-scan cost before a multi-market request. SportsGameOdds, Sportsbook API, and OddsPapi are separate integrations with different coverage and verification limits.
5. Review the status, timestamp, source, exact line, odds, book, BettingPros stars, cover, EV, and **Notes** on each row. In **Settings → API credits & usage**, The Odds API quota is the last Worker snapshot. BettingPros remaining is a Worker-observed rolling estimate, not its official provider balance.

The Board keeps cached lines from other sports when another sport is scanned. A fresh scan can change the prior selection's status. Old prices need a fresh source scan; **Sentinel · Clean cached Board** does not refresh sportsbook quotes or spend BettingPros calls. Previously qualified cached picks can be displayed for review, but a stale price cannot be logged or added to a slip. Started or ESPN-confirmed final games are removed from the pregame Board; use **Live Games** for active games.

## 3. Read screening results

**Passing singles** is the default review. The dropdown can also show **$1 outlier singles**, **Skipped singles**, **Unverified**, **Parlay legs (45–80%)**, or **All statuses**. **PASS only by default**, **Sweet Spot only**, and **Feeling Lucky singles** are display/eligibility controls; read Notes for the actual reason.

Current default single criteria include projected cover of at least **52.5%**, at least **3 BettingPros stars**, a valid exact-line fair probability, odds within the configured range, a net EV floor, and an affordable suggested stake under Kelly, game, and daily limits. Other checks can reject a pick, including duplicate or same-player singles and outlier limits. **Feeling Lucky** optionally allows 45% to below 52.5% cover as a single, with the same star, EV, odds, stake, and bankroll checks. The separate parlay review covers 45% through 80% with positive net EV and other hard checks; a parlay candidate is not automatically a passing single. The cover bands are **Long Shot** (<45%), **Feeling Lucky** (45–<50%), **On the Line** (50–<55%), **Slight Lean** (55–<60%), **Lean** (60–<65%), **Solid** (65–<70%), **Strong** (70–<75%), **Heavy Favorite** (75–<80%), and **High Cover** (80%+). The names describe an individual pick's cover band, not a combined slip probability. The optional Feeling Lucky *single* rule still covers 45–<52.5%, so some On the Line picks can qualify when the toggle is on. The Bet Log's 50–<55% study band separately counts picks below and at/above the 52.5% regular-single cutoff.

An outlier is capped at the configured amount (currently $1) and must still have enough calculated stake and room under caps. The 🎯 Sweet Spot badge marks passing regular singles meeting the preferred stars, EV, odds, and available market comparison; it does not change the stake.

**UNVERIFIED** means the required evidence for a defensible decision is missing, for example an exact-line fair probability or verified matchup. A quoted price or a model EV by itself must not be turned into a made-up probability. DFS pick'em multipliers are not standard American single-bet odds.

## 4. Draft and record bets

- **Single:** On a currently passing, fresh Board row, choose its place/log action. Enter the actual book, American odds, and amount paid, then **Save placed single** only after placing it in the external app. The log counts the stake toward game and daily limits. A price older than 20 minutes must be refreshed before logging through this action.
- **Parlay / pick'em:** Add eligible legs to the Parlay tab. Choose the platform and slip type (power, flex, or standard); enter the actual stake and all-hit multiplier shown in that app. Optional flex partial return and ticket ID can be recorded. **Copy draft** only copies a draft. **I placed this · Log bet** records it as Pending after you actually place it. Check app rules, exact lines, payout tiers, and correlated same-game legs yourself.
- Logged selections are hidden from the default Board view; **Show picks already in Bet log** is available for review. The Board never submits the wager.

## 5. Settle and inspect the log

In **Bet log**, choose a calendar date and use **Refresh game results (ESPN · free)**. For a matching final game and supported exact player stat or market, ESPN grades a single or parlay leg as won, lost, or push. If the matchup, market, or stat cannot be verified, it remains **Pending** with a reason. For a win, enter the **actual amount returned** from the betting app; quoted payout is not assumed to be the settled return. Flex and push returns also need app confirmation. Daily, weekly, and monthly summaries use confirmed returns; Pending is not counted as a loss. The calendar, daily bet-goal bar, and cover-range breakdown show activity by the date placed. **Bet result study** compares each five-point cover band with settled wins and losses, showing average original prediction, observed hit rate, percentage-point difference, pending results, and confirmed single net. Choose Singles or Parlay legs and all dates or a selected day/week/month. Parlay legs have no per-leg profit allocation. Imported bets with no saved original cover stay Unknown; they cannot be backfilled from the final result.

For an existing private bet-import JSON, use **Import placed bets from a file**, review the preview, and then save. Do not commit private bet exports.

## 6. Live Games and sync

**Live Games** embeds the Game Hub. Select a sport and **Scheduled** or **Live**, set the date, and tap a matchup for its gamecast and exact-game prop search. ESPN schedule, scores, play-by-play, and player-stat availability vary by sport and game. The live screen is informational; a prop search still needs an available source quote and the normal screening evidence.

In **Settings → Your bets on every device**, turn on Cloudflare sync using the `SYNC_KEY` configured as a secret on `my-odds-worker`. Set it up on the device holding your existing bets first, then on the phone. The header shows **Sync On/Off**. Sync includes placed singles, slips, draft legs, rules, display preferences, and recent cached Board lines. If sync is off or temporarily unavailable, local browser data remains on that device. Check both devices before relying on shared stake limits.

## 7. Review notes for Alonzo

- Verify probability units end to end: `11%` must be represented as `0.11` internally and displayed as `11%`, never `0.11%`. Inspect BettingPros field normalization, Board math, and rendering with a known example.
- Trace an exact selection from source response → matchup verification → model probability/stars → fee-adjusted EV and stake → single/parlay decision → placed log → ESPN result. Verify no source or market mismatch silently changes its identity.
- Test an actual phone with `PASS`: scroll the Board and embedded Live Games, switch sports and Scheduled/Live, open game details, and confirm the bottom navigation does not trap scrolling.
- Check ESPN final matching and player-stat coverage against actual game results; missing or ambiguous stats should remain Pending. Check that an active game disappears from the pregame Board while its gamecast remains available.
- Confirm that a scan of one sport leaves valid cached picks from another sport visible, and that a fresh scan or elapsed start time handles old picks. Sentinel only re-evaluates cached evidence; it cannot make an old book price current.
- Provider coverage and quota depend on the deployed Worker and provider plans. Tennis/golf are conditional; esports appears only if a provider exposes a league. Real app offerings, DFS multiplier/flex rules, and correlated legs need external verification.
