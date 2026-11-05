<!-- day-nav -->
[← Day 59 — Live video](59-live-video.md) · [Day 61 — Ticket booking →](61-ticket-booking.md)

# Day 60 — Nearby

**Do now**

1. Set a timer.
2. Attempt the problem. Stop at the attempt line. Do not scroll.
3. Then read.

## Time box

35 minutes.

| Minutes | Do this |
|---|---|
| 0–10 | Attempt. What is near me. Index, staleness, privacy. |
| 10–28 | Read. If you leak precise location in logs or responses, fix it. |
| 28–35 | Say the geo index, freshness of movers, and what you refuse to store. |

## Intent

Facing "what is near me," leave able to index points, bound staleness, and avoid leaking a precise location.

## Problem

> Design nearby.
>
> A person asks for points of interest or people around them. Results should be close to their location. Precise location should not leak beyond what the product needs.

## Attempt before reading

10 minutes. Do not scroll.

Write:

1. Query: radius or "nearest K". Latency target.
2. Index: geohash/S2/quad tree — pick one and justify.
3. Moving points (drivers) vs static POIs — update rate.
4. Privacy: precision stored, precision returned, retention.
5. Hot downtown query QPS.

---

**Stop. Geo index with quantized location. Movers update with a bound. Privacy is a requirement, not an appendix.**

---

## Requirements

**In.** Static POIs and optionally moving entities. Query: nearest K within radius R. Results include coarse distance band, not micrometer coordinates of other users.

**Assumptions.** **50 million** POIs; **1 million** movers updating every **5–30 s** when active. Query **20,000**/s peak in dense cities.

**Out.** Full turn-by-turn maps, traffic ML, stalker-ready live sharing without consent controls.

**Privacy.** Store movers at **~100–200 m** quantization for matching unless the product is navigation for that user (their own precise location may stay on device or short TTL). Other users' exact coords never in client responses. Audit logs scrub.

## Estimates

Mover updates: 1e6 / 10 s = **100k**/s peak write — needs a hot geo store, not a single Postgres table with earthdistance for everything.

Queries 20k/s: in-memory/geo-replicated index shards by region.

| Item | Math | Result |
|---|---|---|
| Mover writes | 1M active / 10 s average | **~100k writes/s** |
| Mover memory | 1M × ~64 B (id, cell, coarse lat/lng, ts, status) | **~64 MB** per replica |
| POI memory | 50M × ~200 B (id, cell, name ref, category, rank) | **~10 GB**, a few nodes |
| Query fan-out | 1 km radius, ~0.6–1.2 km cells | **~4–9 cells** per query |
| Cell reads | 20k queries/s × ~9 cells | **~180k cell reads/s** |

**What the table tells you.** Size is small. Mover data fits in RAM on one box. The hard parts are the write rate, the cell reads per query, and a handful of downtown cells that take most of the traffic.

## API and data

`GET /v1/nearby?lat=&lng=&r=&k=` with auth. Server may reduce precision of input for logging.

POI: id, cell_id, attrs. Mover: id, cell_id, updated_at, status.

## Design

### Index

S2/geohash cells; shard by coarse cell. Query fans out to cells covering radius; merge by distance on quantized centers. Cap r and k.

**Pick the cell size from the radius.** Aim for a cell edge about half to one radius, so a query covers roughly 4–9 cells. Too small and one query touches hundreds of cells. Too big and you scan thousands of far-away points, then throw them away.

| Scheme | How it cuts the map | Good at | Watch out for |
|---|---|---|---|
| Geohash | Base32 string; each extra character shrinks the cell. 5 chars ≈ 4.9 × 4.9 km, 6 ≈ 1.2 × 0.6 km, 7 ≈ 153 × 153 m (at the equator) | Prefix range scans in any sorted store; Redis `GEOSEARCH` stores a 52-bit geohash as a sorted-set score | Cells get narrower toward the poles; neighbors across a boundary can share no prefix |
| S2 | Projects the sphere onto a cube, orders cells along a Hilbert curve, levels 0–30, each level quarters a cell's area | Tight "cover this circle" cell sets with mixed levels | Library dependency; harder to eyeball |
| Quadtree | Splits a square into four only where points are dense | Uneven density (Manhattan vs farmland) | Rebalancing as movers shift; harder to shard |
| H3 hexagons | Hex grid; all six neighbors sit at the same distance | Even rings for "within k steps" and heat maps | Children don't nest exactly inside parents |

**Quirk: the boundary miss.** Two cafés 20 m apart can sit on opposite sides of a geohash cell edge and share no prefix at all. Searching only the user's own cell misses the closer one. Always search the cell **plus its 8 neighbors** (or use S2's region cover), then filter by real distance.

**Nearest K, not radius.** Start with a small ring of cells. If you have fewer than K hits, widen one ring and repeat. Stop at K or at the radius cap. Only rank on haversine distance (great-circle distance on a sphere) after you have candidates, since computing it for every point is the scan you built the index to avoid.

**Degrees are not kilometers.** 1° of latitude is about 111 km everywhere. 1° of longitude is about 111 km × cos(latitude): ~111 km at the equator, ~97 km in Delhi, ~69 km in London. A "box ±0.01°" query is not square outside the tropics.

### Movers

Update writes new cell; TTL soft-delete if stale (>2× update period). Clients see last known coarse position within staleness SLO (**15–60 s**).

**Crossing a cell edge.** When a driver moves from cell A to cell B, and A and B live on different shards, the update is two writes: add to B, then remove from A. Between them, the driver shows in both. Readers dedupe by mover id and keep the newest `updated_at`. Do it in that order. Remove-first gives a gap where the driver vanishes, and an empty street is worse than a duplicate you can drop.

**Skip pointless writes.** The phone sends every 5–30 s, but the server only needs to touch the index when the coarse cell changes or the TTL is close to running out. A parked driver refreshes the timestamp instead of rewriting the location. In dense traffic this cuts index writes well below the raw 100k/s.

**Out-of-order updates.** Mobile networks retry and reorder. Store the client's sequence number per mover and drop any update older than the one you hold, or a late packet teleports the driver back two blocks.

```mermaid
sequenceDiagram
  participant D as Driver app
  participant G as Location gateway
  participant B as Shard for new cell B
  participant A as Shard for old cell A
  D->>G: update(id, seq=42, lat, lng)
  G->>G: drop if seq <= last seq; quantize
  G->>B: add id (cell B, ts, TTL)
  G->>A: remove id from cell A
  Note over B,A: reader sees id in both briefly, keeps newest ts
```

### Privacy controls

- Response: bearing/distance bucket, never raw lat/lng of strangers.
- Storage: quantized; precise ephemeral only for self-navigation session.
- Rate limit nearby queries to reduce stalking.
- Opt-in visibility for people nearby.

**Quirk: exact distance is a location leak.** If the API says "Priya is 412 m away," an attacker can fake their own position three times, draw three circles, and find where they cross. Researchers have located dating-app users this way. Rounding the output alone is not enough, and neither is adding fresh random noise to each answer, because the attacker asks 100 times and averages the noise away.

What holds up:

1. **Snap the target, not the answer.** Store the other person at a fixed grid cell center (~100–200 m) and measure from there. Repeated queries always get the same answer, so averaging gains nothing.
2. **Return a band.** "Under 1 km," "1–2 km." Never meters for strangers.
3. **Distrust the asker's location.** Rate-limit how fast an account's claimed position may jump. A user "moving" 5 km between queries 3 seconds apart is spoofing.
4. **Cap queries per target.** Many distance checks on the same person from one account is an alert, not a feature.

### Dense downtown

Hot cells: cache top POIs; movers in separate hotter store. Shed huge r.

**Split the hot cell, don't just add CPU.** If one downtown cell holds 30,000 drivers at a stadium exit, split it into its four children for indexing (S2 and quadtrees make this natural) and spread them across shards. Readers then cover the query circle with the finer cells. Static POIs in the same area are read-only, so add read replicas and cache the top results per cell for 30–60 s.


### Staff arithmetic: mover writes and downtown reads

1e6 movers × update every 10 s ≈ **100k writes/s** into the geo store — that is why movers are not "a Postgres table with earthdistance" alone. Queries 20k/s in dense cities hit hot cells: cache static POIs; keep movers in a hotter path; **cap radius** so a 50 km downtown query cannot fan out to hundreds of cells.

Distance on quantized centers is approximate — good enough for "nearby," wrong for surveying. Say the quantization (~100–200 m for people) as both a privacy and a correctness bound.



### Worked path: downtown query

1. Auth + rate limit; cap `r` and `k`.
2. Cover query circle with S2/geohash cells at chosen resolution.
3. Parallel get static POIs (cached) + movers from hot store for those cells.
4. Merge by distance on quantized centers; scrub foreign coordinates to distance bands; return.

**Self vs other.** Request may include precise self location for ranking; logs store reduced precision. Other people never leave the server as lat/lng.

## Diagrams

```mermaid
flowchart TB
  q[Nearby query] --> cells[Covering cells]
  cells --> shard[Geo shards]
  shard --> merge[Merge top K]
  merge --> scrub[Strip precise coords]
```

Caption: "Cell fan-out, then privacy scrub before the client."

## Failure the user sees

**Stale movers.** Ghost cars; empty street. Show "location may be up to Xs old" (15–60 s SLO). TTL soft-delete if update older than ~2× period — a mover that stopped updating must disappear, not park forever.

**Shard down.** Regional hole; **503 for that area**, not an empty list that looks like "nothing nearby." Empty and outage must differ.

**Privacy leak.** Other users' raw lat/lng in JSON or logs — treat as a Sev-1 product bug, not a style nit. Scrub on the read path; reduce precision in logs.

**Stalking via query hammering.** Rate limit per account/IP; require opt-in for people-nearby; refuse uncapped r/k.

**Huge radius.** Fan-out across too many cells; latency blows. Hard cap r (e.g. 5–10 km for people, larger for POIs if product needs) and k.

## Trade-offs

**Precise vs quantized storage.** Quantized for people-nearby; precise for own session only (device or short TTL).

**Postgres vs dedicated geo.** Static POIs can live in PG with indexes; movers need purpose-built write path.

**Name the refusal inside each alternative.** Against returning strangers' lat/lng: you refuse a stalking API. Against precise storage of all movers forever: you refuse a retention and leak magnet. Against uncapped radius: you refuse downtown meltdown. Against one PG box for 100k mover writes/s: you refuse a known cliff. Against "empty" when the shard is dead: you refuse masking an outage as no results.

**10× movers.** ~1M writes/s — more geo shards by coarse cell; same privacy scrub. Query caps stay.

## Talking points

**If they return other users' lat/lng.** "Privacy bug. Distance bands only."

**If they ask geohash vs S2.** "Either; I need cell cover of radius, shard by coarse cell, merge by approximate distance. The brand matters less than fan-out and scrub."

**If they ask how fresh drivers are.** "Update every 5–30 s when active; soft-delete after ~2× period; UI bound 15–60 s."

**If they ask about their own navigation blue dot.** "Precise can stay on device or short-TTL server session. That is not what we return about other people."

**If they ask what pages.** "Mover write lag, query p99 in hot cells, and — separately — any response analytics that show raw foreign coordinates (should be zero)."

## Say this in the room

Nearby queries hit a cell-based geo index sharded by region — geohash or S2 — with radius capped and results merged by approximate distance on quantized centers. About a million movers updating every ten seconds is roughly 100k writes a second into a hot geo store, not a single earthdistance table. Static POIs are colder and cacheable downtown. I store and return quantized locations for other people, never raw coordinates, scrub logs, and rate-limit queries so the API is not a stalking tool. A dead shard is a 503 for that area, not an empty list.

### Staff depth: privacy is a read-path scrub

Quantize movers for matching (~100–200 m). Responses return distance bands, never strangers' lat/lng. Rate-limit nearby to reduce stalking. Opt-in for people-nearby.

**Hot downtown.** Cache static POIs; movers in a hotter store; cap radius.

**Stale vanish.** Soft-delete movers that miss ~2× update period so ghosts do not park forever.

**What staff sounds like.** 100k mover writes/s said once; cell fan-out plus scrub; empty≠outage; refuse precise leaks in logs too.

### More probes, with the answer

**"What pages?"** Name the user-visible lag or error metric from Failure — not only CPU. **"What do you refuse?"** Pick one refusal from Trade-offs and say the lie it prevents. **"What is the sensitive assumption?"** The estimate that flips the design if wrong by 10×.

## Kit artifact

| Follow-up | You answer with |
|---|---|
| "How precise?" | Quantization for others; self precise only with short TTL / on device. |
| "Geohash edge case?" | Two close points can share no prefix; search cell + 8 neighbors, then filter by real distance. |
| "Nearest K, sparse area?" | Expanding rings of cells until K hits or the radius cap. |
| "Driver crosses shards?" | Add to new cell, then remove from old; readers dedupe by id, newest timestamp wins. |
| "Can't someone triangulate?" | Snap targets to a fixed grid, return bands, rate-limit position jumps and per-target queries. |
| "Downtown hot spot?" | Split the hot cell into children across shards; cache static POIs 30–60 s. |

## Design log

One line: your cell scheme and the privacy scrub you put on the response.

Next: [Day 61 — Ticket booking](61-ticket-booking.md). Assigned seats, hold TTL — not day 49 inventory.

---

<!-- day-nav -->
[← Day 59 — Live video](59-live-video.md) · [Day 61 — Ticket booking →](61-ticket-booking.md)
