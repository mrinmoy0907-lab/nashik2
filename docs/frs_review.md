# FRS v0.6 vs CLAUDE.md — conflicts and gaps

Source: `docs/FRS_NFR_v0.6.pdf` ("Kumbh DT Portal FRS NFR PoC Review Updates", 39 pp).
Rule (CLAUDE.md §1): where they disagree, the FRS wins. Items marked **Conflict** need CLAUDE.md updated once approved.

## Conflicts (FRS wins)

| # | Topic | CLAUDE.md | FRS | Resolution |
|---|---|---|---|---|
| C1 | Camera event fields | §8: `ts`, `type`, `snapshot`, `foot_px` | §11: `camera_id, time (ISO 8601 IST), event_type, count, density, bounding box, confidence, snapshot_url`; optional `foot_point_px`, `ground_xy [lon,lat]`, `roi_id`, `direction_deg` | Use FRS names in `sim/contract.py`; update CLAUDE.md §8 |
| C2 | Event types (UC-12) | crowd_high, fall, intrusion, loitering, abandoned_object, women_safety, vehicle | crowd, fall, women hailing / isolation, loitering, abandoned object, watch-list match, vehicle | Add `watchlist_match` (Police roles only, event + evidence link only); keep `intrusion` as river-edge danger-strip event (P-09 danger zones) |
| C3 | Time-series store | Plain Postgres; TimescaleDB optional | UC-14: versioned (bitemporal) layer tables + time-series store (e.g. TimescaleDB) | Use TimescaleDB hypertable for `zone_density` and events; zones/assets carry valid-from/to + recorded time |
| C4 | 3D buildings delivery | (not specified; plan used GeoJSON extrusion) | §7 / §12: 3D Tiles 1.1 streaming; CityGML 2.0 / CityJSON for semantic 3D | Buildings → 3D Tiles 1.1 from PostGIS (pg2b3dm); GeoJSON extrusion only as fallback |
| C5 | Layer services | FastAPI GeoJSON | §11–13: OGC WMS / WFS (OGC API where available), OpenAPI | Add GeoServer for static layers (WMS/WFS); FastAPI for live data, analytics, WebSocket |
| C6 | Escalation | Ack/resolve timers only | P-04: High not acked 2 min → zone in-charge; 5 min → control-room supervisor | Two-step escalation chain in config |

## Scope differences (no change unless you say so)

| Item | CLAUDE.md demo | FRS wave |
|---|---|---|
| UC-07, UC-09, UC-11, UC-12, UC-14 | Live in demo | Wave 2 |
| UC-13 traffic / parking | Live in demo | Wave 3 |
| AI assistant | Roadmap only | Wave 2 |

CLAUDE.md shows more than FRS Wave 1. That fits the 60-minute demo, but the walkthrough note should say which wave each item belongs to.

## Requirements CLAUDE.md does not mention (to be built in)

- **P-01:** zones carry `level`, `valid_from` / `valid_to`, Draft → Active status (R3 Confirm), owner department (mandatory); no overlaps / gaps at the same level; obstructions layer; **water-level layer** (step tier elevations + gauge → submerged steps drop out of net area, danger strip follows water line).
- **P-09:** calibration is versioned: GCPs (≥ 6), homography per camera / PTZ preset, reprojection error ≤ 0.5 m at 30 m, VA ROI, effective counting range, gate / queue-exit tripwires, danger zones, R8 approval. Earlier versions kept for replay.
- **P-03:** exponential smoothing of zone density; 10-min trend; direction of movement.
- **P-05:** OTP login per shift; location consent; photo required to close High tasks.
- **P-06:** stale after 2 × expected interval; feed lineage (source → layer).
- **P-07:** low / expected / high range; forecast-vs-actual error on admin screen; refresh every 5 min.
- **UC-06:** R3 has 3 min to approve, then operator override if Critical (logged); holding area at 90 % → suggest next.
- **UC-07:** case photos visible to R2 / R6 only, deleted on close; no search without case number.
- **UC-08:** fall in river-edge danger zone raised one severity higher; 3 m corridor.
- **§9:** band table also covers capacity-used % (< 70 / 70–90 / 90–100 / > 100) and risk score (0–39 / 40–59 / 60–74 / 75–100); status flows for alert, missing person, diversion, temporary asset.
- **§12:** fixed ID keys `zone_id, camera_id, gate_id, asset_id, alert_id`; ISO 19115 metadata per dataset; vertical datum to be fixed by survey.
- **§13 NFR:** two-factor login for admin and senior roles; GIGW 3.0 / WCAG 2.1 AA; English first, Hindi / Marathi ready; satellite-only areas labelled "≈ 1–2 m accuracy".

## Items that need NTKMA or a field survey (add to `docs/data_queries.md`)

- P-01 acceptance: net area within 5 % of an **independent field measurement** (DGPS / tape).
- P-09 acceptance: GCPs from a drone ortho; camera survey (height, azimuth, tilt, FoV).
- §7: LoD3 ghats, steps, temple exterior, Kund — public data only gives LoD1 extrusions.
- §13: ortho ≤ 30 cm; public Sentinel-2 is 10 m (label on screen).
