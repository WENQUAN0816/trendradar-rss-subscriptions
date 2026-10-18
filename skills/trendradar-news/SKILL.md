---
name: trendradar-news
description: Use when the user asks for today's or recent news, current热点, a personalized news brief, or the local TrendRadar RSS portal; use configured TrendRadar data without opening article pages.
metadata:
  short-description: Personalized TrendRadar news and RSS portal
---

# TrendRadar News

## Core Rules

- Treat configured feeds and stored freshness as separate checks. An empty result never means no feeds are configured.
- Use TrendRadar MCP for ordinary reads. If configured RSS is stale or empty for the requested date, run the full app from `C:\Users\Administrator\.cc-switch\apps\TrendRadar`:

  ```powershell
  .\.venv\Scripts\python.exe -X utf8 -u -m trendradar
  ```

- MCP `trigger_crawl` retrieves hotlists only; it cannot refresh or prove RSS coverage.
- For RSS editorial processing, send the model only each item's title and source name. Never open links, read article bodies, or send feed-provided summaries.
- Report fetched rows, stored/deduplicated rows, and chat-selected rows separately. Never present a short brief as the full corpus.

## Personalized Brief

Use the configured topic order in `config/frequency_words.txt`. Group by the best-fit theme, merge duplicate reports, keep source links, and write concise Chinese factual titles and title-level takeaways. Omit empty themes and distinguish facts from interpretation.

## Local RSS Portal

After a requested refresh, generate and verify:

`C:\Users\Administrator\.cc-switch\apps\TrendRadar\output\html\latest\current.html`

This is the fixed current portal; dated HTML files are archives.

Portal behavior:

- Keep all fetched RSS items through `display.standalone.rss_feeds: ["*"]` and `max_items: 0`.
- Optimize for desktop and render one continuous column.
- Default to the theme view; provide a separate fully expanded RSS-source view.
- Make every primary title Chinese. Common acronyms and proper nouns may remain. `USM` means `马来西亚理科大学`; `UM` means `马来亚大学`.
- Merge reports of the same event in theme view and retain every contributing source link.
- Give every nonempty theme up to 20 ranked `本主题今日重磅` events, followed by nonduplicated `更多新闻`.
- Build the overall `今日重磅` from the top 3 events of every nonempty theme; it may exceed 20.
- Highlight attention-worthy title keywords in bold red without another model call.
- Do not render a separate AI-analysis section. A title-level takeaway may use only information already present in the title.
- Cache editorial results by stable source plus normalized title and process only unseen items.
- Call keyword-filter counts `关键词匹配`, never `热榜` or `重磅`; importance rankings are editorial scores, not feed order or match counts.

After generation, verify total source-view items, merged-event counts, per-theme limits, overall top-3 composition, Chinese primary titles, absence of the AI-analysis section, and no horizontal overflow. Give the user the clickable local file link and open it only when requested.

## Feed Health

`github-trending-daily` must use `https://mshibanami.github.io/GitHubTrendingRSS/daily/all.xml`. Do not restore the Cloudflare-blocked RSSHub URL. A valid regression requires HTTP 200, successful parsing, nonempty entries with `github.com` links, and inclusion in the full-crawl success count.

## Boundaries

Do not alter feed lists, scheduling, or notifications for an on-demand brief. Do not silently substitute hotlists for missing RSS. If requested historical dates were never stored, state the exact gap after attempting the correct refresh.
