# Evaluation test set

Fill this in after every refresh, before you publish. The agent is the system under test.
You are the grader. Nothing here is automated on purpose: the point is to decide, with a
source you trust, whether the agent told you the truth.

**Ground truth sources.** Pick one and use the same one every time, so your scores are
comparable week to week:

- Prices: Google Finance (google.com/finance), Yahoo Finance, or your brokerage app.
- News: the article itself. Open the link. Check the date on the page.
- Calendar: the company's investor relations page (search "TICKER investor relations
  events"), or the Fed's own meeting calendar for FOMC dates.

Refresh date: 2026-10-28    Grader: number-checker

## A. Prices (one row per ticker)

Pass if the dashboard's last price is within $0.10 of the ground truth for the same day
and the same price type (close vs intraday). If the agent recorded an intraday price,
compare against the intraday price at about the same time; do not compare it to a close.

Google Finance would not serve data to my fetch tool ("Google Finance isn't supported on this device"), so every row uses one named backup, stockanalysis.com history pages. Note this is the same site the agent used. The five closes (Oct 21 to Oct 27) all matched the backup exactly for all five tickers. The backup page showed Oct 28 intraday at about 10:54 AM EDT, later than the dashboard times (10:24 to 10:38), and I could not get a time-matched quote, so a gap over $0.10 is recorded as Fail with that caveat.

| Ticker | Date shown | Price type shown | Dashboard price | Ground truth price | Source you used | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | 2026-10-28 10:38 | intraday | 218.00 | 217.71 (about 10:54 ET, later snapshot; diff 0.29). Closes Oct 21 to 27 all match. | https://stockanalysis.com/stocks/nvda/history/ | Fail |
| DELL | 2026-10-28 10:38 | intraday | 494.60 | 492.00 (about 10:54 ET, later snapshot; diff 2.60). Closes Oct 21 to 27 all match. | https://stockanalysis.com/stocks/dell/history/ | Fail |
| CAT | 2026-10-28 10:36 | intraday | 710.83 | 708.06 (later snapshot; diff 2.77). Closes Oct 21 to 27 all match. | https://stockanalysis.com/stocks/cat/history/ | Fail |
| HLI | 2026-10-28 10:24 | intraday | 134.65 | 134.32 (later snapshot; diff 0.33). Closes Oct 21 to 27 all match. | https://stockanalysis.com/stocks/hli/history/ | Fail |
| PG | 2026-10-28 10:37 | intraday | 159.20 | 159.16 (diff 0.04). Closes Oct 21 to 27 all match. | https://stockanalysis.com/stocks/pg/history/ | Pass |

Rerun (BRIEF.asOf 2026-10-27, priceType close, latest null for all). Google Finance was tried first (https://www.google.com/finance/quote/NVDA:NASDAQ) and again said "Google Finance isn't supported on this device", so it shows no dates. Backup: stockanalysis.com history pages (same site the agent used, so not independent). All 25 closes, Oct 21 to Oct 27, matched the backup to the cent. Last close compared with the Oct 27 close.

| Ticker | Date shown | Price type shown | Dashboard price | Ground truth price | Source you used | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA (rerun) | 2026-10-27 | close | 217.94 | 217.94 (diff 0.00). Other four closes match: 221.97, 212.21, 210.96, 216.00. | https://stockanalysis.com/stocks/nvda/history/ | Pass |
| DELL (rerun) | 2026-10-27 | close | 501.96 | 501.96 (diff 0.00). Other four closes match: 485.88, 452.95, 456.44, 467.09. | https://stockanalysis.com/stocks/dell/history/ | Pass |
| CAT (rerun) | 2026-10-27 | close | 713.31 | 713.31 (diff 0.00). Other four closes match: 740.21, 704.05, 687.00, 692.13. | https://stockanalysis.com/stocks/cat/history/ | Pass |
| HLI (rerun) | 2026-10-27 | close | 132.35 | 132.35 (diff 0.00). Other four closes match: 128.39, 133.24, 131.31, 131.21. | https://stockanalysis.com/stocks/hli/history/ | Pass |
| PG (rerun) | 2026-10-27 | close | 159.23 | 159.23 (diff 0.00). Other four closes match: 156.84, 165.59, 161.02, 159.92. | https://stockanalysis.com/stocks/pg/history/ | Pass |

## B. Weekly change (one row per ticker)

The page computes this from the five closes. Pass if it is within 0.3 percentage points of
your own calculation: (last price minus the close five trading days earlier) divided by
that earlier close.

Method: last close (Oct 27) minus the close five trading days earlier (Oct 20, from the same backup page), divided by Oct 20. The page itself measures from the Oct 21 close to the Oct 28 intraday price (see the render code), so I worked the dashboard value out that way. For reference I also give Oct 21 to Oct 27 close.

| Ticker | Dashboard weekly change | Your calculation | Pass / Fail |
|---|---|---|---|
| NVDA | (218.00 - 221.97) / 221.97 = -1.79% | (217.94 - 216.47) / 216.47 = +0.68%. Oct 21 to Oct 27 close: -1.82% | Fail (https://stockanalysis.com/stocks/nvda/history/) |
| DELL | (494.60 - 485.88) / 485.88 = +1.79% | (501.96 - 461.56) / 461.56 = +8.75%. Oct 21 to Oct 27 close: +3.31% | Fail (https://stockanalysis.com/stocks/dell/history/) |
| CAT | (710.83 - 740.21) / 740.21 = -3.97% | (713.31 - 725.24) / 725.24 = -1.65%. Oct 21 to Oct 27 close: -3.63% | Fail (https://stockanalysis.com/stocks/cat/history/) |
| HLI | (134.65 - 128.39) / 128.39 = +4.88% | (132.35 - 130.33) / 130.33 = +1.55%. Oct 21 to Oct 27 close: +3.08% | Fail (https://stockanalysis.com/stocks/hli/history/) |
| PG | (159.20 - 156.84) / 156.84 = +1.50% | (159.23 - 159.32) / 159.32 = -0.06%. Oct 21 to Oct 27 close: +1.52% | Fail (https://stockanalysis.com/stocks/pg/history/) |

Cause: BRIEF holds only Oct 21 to Oct 27 (four trading-day intervals) plus a partial day, so the page cannot show a true five-trading-day change. The Oct 20 close is missing from BRIEF.

Rerun method: dashboard value is worked out from the BRIEF closes (Oct 27 close minus Oct 21 close, over Oct 21), which is what the page measures with no latest price. My value follows the spec literally: Oct 27 close minus Oct 20 close, over Oct 20, using Oct 20 closes seen on the backup page. Oct 20 closes: NVDA 216.47, DELL 461.56, CAT 725.24, HLI 130.33, PG 159.32. BRIEF has no Oct 20 close for any ticker.

| Ticker | Dashboard weekly change | Your calculation | Pass / Fail |
|---|---|---|---|
| NVDA (rerun) | (217.94 - 221.97) / 221.97 = -1.82% | (217.94 - 216.47) / 216.47 = +0.68%. Gap 2.50 points. | Fail (https://stockanalysis.com/stocks/nvda/history/) |
| DELL (rerun) | (501.96 - 485.88) / 485.88 = +3.31% | (501.96 - 461.56) / 461.56 = +8.75%. Gap 5.44 points. | Fail (https://stockanalysis.com/stocks/dell/history/) |
| CAT (rerun) | (713.31 - 740.21) / 740.21 = -3.63% | (713.31 - 725.24) / 725.24 = -1.65%. Gap 1.99 points. | Fail (https://stockanalysis.com/stocks/cat/history/) |
| HLI (rerun) | (132.35 - 128.39) / 128.39 = +3.08% | (132.35 - 130.33) / 130.33 = +1.55%. Gap 1.53 points. | Fail (https://stockanalysis.com/stocks/hli/history/) |
| PG (rerun) | (159.23 - 156.84) / 156.84 = +1.52% | (159.23 - 159.32) / 159.32 = -0.06%. Gap 1.58 points. | Fail (https://stockanalysis.com/stocks/pg/history/) |

Rerun cause: unchanged. The page's number is an Oct 21 to Oct 27 move (four trading-day intervals). The first row of each closes list is Oct 21, so the Oct 20 close is missing from BRIEF. The partial-day problem from the first grading is gone, but the five-day window is still wrong.

## C. News grounding (one row per news item, three per ticker)

Open every link. Pass only if all four are true: the link opens, the headline matches the
article, the article date is within the last seven days, and the "why it matters" line is
supported by the article (not just plausible).

| Ticker | Headline (first few words) | Link opens | Headline matches | Dated within 7 days | "Why" is supported | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | Nvidia Stock News (Oct 27, 2026): NVDA Rises 2.4%... | Yes | Yes | Yes, Oct 27 | No. Article says Meta's filing names Nvidia, AMD and Broadcom who "indicated an interest in buying up to an aggregate $5.0 billion"; the brief attributes the whole $5.0 billion to Nvidia. The 2.4% rise and non-binding point are supported. | Fail (https://www.heygotrade.com/en/news/nvidia-nvda-stock-news-october-27-2026/) |
| CAT | Can Caterpillar's $72B Backlog Save... | Yes | Yes | Yes, Oct 25 | Yes: "dropped 15.4% over the past month", $72 billion backlog, tariff costs "around $2.2 billion" | Pass (https://247wallst.com/investing/2026/10/25/can-caterpillars-72b-backlog-save-its-stock-from-a-10-slump/) |
| PG | P&G Announces Fiscal Year 2027 First Quarter Results | Yes | Yes | Yes, Oct 22 | Yes: "Net sales rose 4.0% to $23.3 billion", core EPS $2.00, sales guidance 2% to 4% reflecting Thorne | Pass (https://www.sec.gov/Archives/edgar/data/0000080424/000008042426000250/fy2627q1jas8-kexhibit991.htm) |

DELL and HLI have no news items in BRIEF (empty lists); no rows to grade.

Rerun (window for "within seven days" is Oct 20 to Oct 27). HLI now has an empty list again, nothing to grade. DELL has a new item.

| Ticker | Headline (first few words) | Link opens | Headline matches | Dated within 7 days | "Why" is supported | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA (rerun) | Nvidia Stock News (Oct 27, 2026): NVDA Rises 2.4%... | Yes | Yes | Yes, Oct 27 | Yes. "indicated an interest in buying up to an aggregate $5.0 billion" for Nvidia, AMD and Broadcom together; "not binding agreements"; "rose 2.39% to close at $216.00"; "a rebound in AI-linked chip stocks". The brief's no-split wording matches. | Pass (https://www.heygotrade.com/en/news/nvidia-nvda-stock-news-october-27-2026/) |
| DELL (rerun) | Dell Technologies Inc Stock (DELL) Moved Up by 4.03%... | Yes | Yes | Yes, Oct 27, 2:15 PM | Mostly, but one claim fails. Drivers are supported (enterprise AI adoption, Dell AI Factory with NVIDIA, large AI server backlog). The why line says "The 4.03% is the article's intraday figure", but the article gives no time window for the 4.03% (it only mentions "pronounced intraday volatility"). The article is also a TradingKey aggregator piece. | Fail (https://tradingkey.com/news/market-movers/262242529-market-movers-dell-20261027) |
| CAT (rerun) | Can Caterpillar's $72B Backlog Save... | Yes | Yes | Yes, Oct 25 | Yes. "Shares have dropped 15.4% over the past month", $72 billion backlog, tariff costs about $2.2 billion for the full year. The article elsewhere says 8.72% over thirty days, so its own figures differ, but the brief's "about 15%" matches a quoted line. | Pass (https://247wallst.com/investing/2026/10/25/can-caterpillars-72b-backlog-save-its-stock-from-a-10-slump/) |
| PG (rerun) | P&G Announces Fiscal Year 2027 First Quarter Results | Yes | Yes | Yes, Oct 22 | Yes. "Net sales rose 4.0% to $23.3 billion", core EPS $2.00, guidance 2% to 4% updated for the "acquisition of Thorne". | Pass (https://www.sec.gov/Archives/edgar/data/0000080424/000008042426000250/fy2627q1jas8-kexhibit991.htm) |

(Add rows as needed. Fifteen items is normal for five tickers.)

## D. Calendar (one row per event)

Pass if the event and its date appear on the source linked, and the date is in the future
relative to the refresh date.

| Ticker | Event | Date shown | Confirmed on source | Pass / Fail |
|---|---|---|---|---|
| NVDA | FOMC meeting Oct 27-28, decision day | 2026-10-28 | Yes, Fed page lists Oct 27-28 with no asterisk or tentative label for 2026. Date equals refresh date (on or after). | Pass (https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm) |
| CAT | Q3 2026 results, before market open | 2026-10-29 | Company page timed out twice (could not open). A search summary of that page said 5:30 a.m. CDT Oct 29, but I did not see the page itself. | Fail, could not open (https://www.caterpillar.com/en/news/corporate-press-releases/h/caterpillar-to-announce-3q-2026-financial-results-on-oct-29.html) |
| HLI | Q2 FY2027 results after close, call 5:00 p.m. ET | 2026-10-28 | Brief cites a Finviz copy, a third-party aggregator. The hl.com release page returned empty content to me, so the company page is not confirmed. Finviz text itself says Oct 28, after NYSE close, call 5:00 p.m. ET. | Fail, aggregator source and company page not confirmed (https://finviz.com/news/400062/houlihan-lokey-announces-release-date-for-second-quarter-results-for-fiscal-year-2027) |

Rerun (refresh date 2026-10-28). Both company-page checks failed again. The brief now says "Not confirmed on ... own page" for CAT and HLI, which is honest, but the rule still requires the company page to list the event.

| Ticker | Event | Date shown | Confirmed on source | Pass / Fail |
|---|---|---|---|---|
| NVDA (rerun) | FOMC two-day meeting (Oct 27-28) ends; rate decision due today | 2026-10-28 | Yes. Fed page lists October 27-28 with no asterisk and no tentative label for 2026 (tentative language appears only for 2027). Date equals refresh date. | Pass (https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm) |
| CAT (rerun) | Q3 2026 results 5:30 a.m. CDT, call 7:30 a.m. CDT | 2026-10-29 | Caterpillar's press release page timed out again (could not open). investors.caterpillar.com/events-and-presentations returned 404. A search summary of the company page says Oct 29, 5:30 a.m. CDT, but I did not see that page. The brief links the Finviz copy (opened, says Oct 29, 5:30 a.m. CDT, published Oct 15), a third-party aggregator. | Fail, could not open company page; aggregator source (https://finviz.com/news/402910/caterpillar-inc-to-announce-third-quarter-2026-financial-results-on-october-29) |
| HLI (rerun) | Q2 FY2027 results after NYSE close, call 5:00 p.m. ET | 2026-10-28 | hl.com release page returned empty content again (could not open). investors.hl.com events URL returned 404. Finviz copy (opened) says Oct 28, after NYSE close, call 5:00 p.m. ET, published Oct 7. A search summary of hl.com agrees, but I did not see the page. Aggregator source. | Fail, could not open company page; aggregator source (https://finviz.com/news/400062/houlihan-lokey-announces-release-date-for-second-quarter-results-for-fiscal-year-2027) |

## E. Omissions

Did the agent miss something a human analyst would have included? List anything you found
in the ground-truth sources that the brief does not mention (a big headline, an earnings
date inside the window). Each one counts as a Fail in the score below.

- 
- 

## F. Your own test cases

Write two tests of your own that none of the sections above cover. Examples students have
used: "the as-of date on the page matches the changelog date", "no ticker shows a flat
sparkline when the real stock moved", "the page still renders if I delete one ticker's
news list in the data". Write the test, run it, record the result.

| Test | How you checked | Pass / Fail |
|---|---|---|
| | | |
| | | |

## Score

| Section | Passes | Total | Rate |
|---|---|---|---|
| A. Prices | 1 | 5 | 20% |
| B. Weekly change | 0 | 5 | 0% |
| C. News | 2 | 3 | 67% |
| D. Calendar | 1 | 3 | 33% |
| A. Prices (rerun) | 5 | 5 | 100% |
| B. Weekly change (rerun) | 0 | 5 | 0% |
| C. News (rerun) | 3 | 4 | 75% |
| D. Calendar (rerun) | 1 | 3 | 33% |
| E. Omissions (fails only) | 0 | | |
| F. Your own | | | |
| **Overall** | | | |

Publish only if A and B are 100% and C is at least 80% after fixes. If a row failed, tell
Claude exactly which row and what the correct value is, let it fix only that, rerun the
`qa-reviewer` agent, and regrade that row. Keep the original Fail in this file: a record of
what the agent got wrong is more useful than a clean sheet.
