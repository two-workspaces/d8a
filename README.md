# Deploy and Host d8a on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/d8a?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=d8a)

[d8a](https://d8a.tech) is open-source, warehouse-native web analytics that speaks the Google Analytics 4 protocol: point your existing GA4 tag (gtag.js or Google Tag Manager) at it, and every event lands as a row in your own ClickHouse, already joined into sessions with source, medium, landing page, country, device and browser. No sampling, no data retention limits, no Google. This template pairs it with ClickHouse and with Blazer for dashboards, so you get reports on day one and plain SQL on your raw events.

## About Hosting d8a

Four services:

- **d8a**: the official image, pinned to a release, on your Railway domain. It receives GA4 hits, builds sessions and writes them to ClickHouse. Its volume keeps the in-flight sessions and the DB-IP geolocation database across restarts.
- **ClickHouse**: the events table. Private, with a generated password and its own volume, sized for a service rather than the 48 cores Railway's hosts report.
- **Blazer**: SQL dashboards on top, behind a password, with a ready-made "Website analytics" dashboard: visitors, sessions and pageviews by day, top pages, sources, landing pages, countries, devices and browsers, all with a date-range picker.
- **Postgres**: Blazer's own saved queries and dashboards.

What the template adds:

- **Real visitor locations.** d8a trusts Railway's edge for the visitor's address, so countries are the visitors', and the last octet is masked before storage.
- **Read-only dashboards.** Blazer connects to ClickHouse as its own read-only user, so a query typed in Blazer can't change or delete events.

## Common Use Cases

- Keeping GA4 tracking but owning the data, without GA4's sampling and retention limits
- Website analytics you query with SQL, next to your other data
- Replacing Google Analytics without retagging your site

## Dependencies for d8a Hosting

- ClickHouse 26.3 and Postgres 17 (included, private network only)

### Deployment Dependencies

- [d8a documentation](https://docs.d8a.tech)
- [Template source on GitHub](https://github.com/two-workspaces/d8a)

### Implementation Details

**Send events.** Follow [d8a's GA4 guide](https://docs.d8a.tech/articles/sources/ga4): a Google Tag Manager tag that copies your GA4 hits to d8a while GA4 keeps working, or a second `gtag('config', ...)` call. Wherever the guide shows `https://global.t.d8a.tech/<property_id>/g/collect`, use `https://<your d8a domain>/g/collect`.

**See reports** at the Blazer service's domain, signing in with `BLAZER_USERNAME` and `BLAZER_PASSWORD` from its Variables tab. Sessions appear once they end: after `SESSIONS_TIMEOUT` (30 minutes) without a hit, plus up to a minute.

**Write your own queries.** In Blazer, pick the `d8a` data source. Everything is in the `events` table: one row per event, with the session's columns (`session_source`, `session_entry_page_location`, `session_geo_country`, ...) on every row. Use `{start_time}` and `{end_time}` to get a date-range picker.

**Other tools.** ClickHouse is private; to reach it from outside Railway, add a TCP proxy to it in Settings → Networking.

**Telemetry.** d8a sends anonymous usage pings (version and uptime only) to d8a.tech by default; see its docs to turn them off.

**Memory.** About 1.1 GB across the four services: ClickHouse 0.7 GB, Blazer and d8a 0.15 GB each, Postgres under 0.1 GB. ClickHouse grows with traffic and query load.

## Why Deploy d8a on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying d8a on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
