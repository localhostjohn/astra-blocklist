# Streaming-safe AdGuard Home baseline

## Objective

**Maximum safe blocking, minimum service disruption.**

The Astra DNS policy blocks advertising, tracking, telemetry and malicious domains where DNS-level filtering can do so safely. It does not attempt to force DNS filtering to remove adverts that share delivery infrastructure with legitimate streaming content.

The hard acceptance criterion is that normal browsing, authentication and streaming remain reliable.

## Current deployment assumptions

- AdGuard Home runs on the Astra Raspberry Pi.
- AdGuard Home is deployed in Docker using host networking.
- The Pi provides DNS filtering to selected LAN clients.
- Tailscale remains the private remote-management boundary.
- The AdGuard administration interface must not be exposed directly to the public internet.

## Recommended baseline filter stack

Use **one** HaGeZi Multi tier. Do not stack Normal, Pro, Pro++ and Ultimate together because the higher tiers already contain the lower tiers.

### 1. HaGeZi Multi NORMAL

Name:

```text
HaGeZi Multi NORMAL
```

URL:

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/multi.txt
```

Reason: Normal is intended to provide broad ad/tracker/telemetry blocking with a low risk of restrictions. That matches Astra's compatibility-first requirement better than immediately deploying Pro++, Ultimate or highly aggressive native-tracker lists.

### 2. HaGeZi Threat Intelligence Feed MINI

Name:

```text
HaGeZi TIF MINI
```

URL:

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/tif.mini.txt
```

Reason: adds a smaller malware/phishing threat feed without initially placing the memory and parsing load of the full TIF list on a multi-service Raspberry Pi.

### 3. Astra Custom Blocklist

Name:

```text
Astra Custom Blocklist
```

URL:

```text
https://raw.githubusercontent.com/localhostjohn/astra-blocklist/main/astra-blocklist.txt
```

The Astra list is intentionally conservative. Streaming/app telemetry rules with greater breakage potential remain commented out until tested.

## Do not enable initially

Do not initially add:

- HaGeZi Pro++, Ultimate, or multiple Multi tiers;
- blanket Amazon, Google, Microsoft, Samsung, LG or other native-tracker lists;
- broad CDN blocks;
- core Netflix, YouTube, Prime Video or Sky domains;
- encrypted-DNS bypass blocking;
- speculative domains copied from forum posts.

These can be evaluated later as controlled experiments.

## AdGuard Home configuration procedure

1. Open **Filters → DNS blocklists**.
2. Record the current enabled lists before changing anything.
3. Add HaGeZi Multi NORMAL.
4. Add HaGeZi TIF MINI.
5. Keep or add Astra Custom Blocklist.
6. Update all lists.
7. Confirm filtering is enabled.
8. Open **Query Log** and verify requests from the intended client devices appear.
9. Do not add additional lists during the first validation window.

## Named clients

Where practical, give important devices stable DHCP reservations and add them as named AdGuard Home clients. Suggested names:

- `sky-glass`
- `windows-main`
- `macbook`
- `phone`
- `tablet`

Named clients make Query Log evidence and per-client rules much easier to interpret.

## Streaming validation matrix

Run one service at a time while watching Query Log.

| Service | Test | Pass criteria |
| --- | --- | --- |
| Sky Glass | Home screen, guide, live channel, on-demand programme | UI loads; playback starts and remains stable |
| YouTube | Home feed, search, video start, seek, another video | No playback or sign-in failure |
| Netflix | Launch, profile, browse, start programme, seek | No unexpected error or playback failure |
| Prime Video | Launch, browse, programme start, seek | No unexpected error or playback failure |
| General web | Several normal websites and sign-ins | No broken login or essential content |
| Device services | App updates / time / normal device functions | No obvious regression |

The presence of an advert does **not** mean the DNS test failed. DNS cannot reliably remove in-stream advertising when ad and content traffic use the same service infrastructure.

## Query Log triage

If a service fails immediately after a filter change:

1. Reproduce the failure once.
2. Note the client and exact time.
3. Filter Query Log to that client.
4. Look for blocked requests immediately before the failure.
5. Identify which list or custom rule caused the block.
6. Temporarily unblock only the suspected domain.
7. Repeat the exact test.
8. If functionality returns, create the narrowest permanent allowlist rule.
9. Record the evidence and reason.

Example allowlist syntax:

```text
@@||example.com^
```

Avoid allowlisting an entire vendor or CDN unless evidence shows that scope is genuinely necessary.

## Escalation path

After the baseline is stable:

1. Review the top blocked domains by Sky Glass.
2. Classify candidates as advertising, analytics, telemetry, CDN, authentication, playback or unknown.
3. Test one additional rule at a time.
4. Keep a rule only if the targeted request is blocked without harming service function.
5. Consider moving from HaGeZi Normal to Pro only after the baseline has proven stable and rollback is understood.

Do not jump directly to Ultimate simply to increase the blocked-query percentage.

## Browser/device layer

DNS is only layer one. Where supported:

- use a reputable browser content blocker on desktop browsers for page-level and same-origin advertising that DNS cannot distinguish;
- retain DNS-level protection for the whole network;
- avoid attempting to intercept/decrypt streaming TLS traffic.

This layered design improves blocking without turning AdGuard Home into a single point of application breakage.

## Evidence to retain

For a portfolio-quality change, record:

- date/time of filter change;
- enabled list names and versions where available;
- Pi memory/CPU before and after;
- DNS resolution test;
- screenshots or notes for Query Log;
- Sky Glass/YouTube/Netflix/Prime validation outcome;
- false positives and allowlist rules;
- rollback result, if exercised.

Never publish private IP addresses, tokens, credentials, raw query logs containing sensitive browsing history, or private DNS names.
