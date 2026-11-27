# CLAUDE.md — Nashik–Trimbakeshwar Kumbh 2027 Digital Twin · EoI PoC

> Paste this file as `CLAUDE.md` in the repo root of the Nashik Claude Code session.
> It is the single source of context for every build session. Update the "Data status" table as data arrives.

## 1. What we are building and why

- **Client:** Nashik Trimbakeshwar Kumbh Mela Authority (NTKMA). EoI Ref. IT/001/2026-2027/NTKMA — "Design, Development and Implementation of Comprehensive Digital Twin for Kumbh Mela 2027".
- **Bidder:** AMNEX Infotechnologies. Platform: Spectator (AMNEX's own 3D Digital Twin platform).
- **What this repo is:** a working PoC web portal for the **live demo (Category C3 — 40 of 100 technical marks)**, built on the dataset NTKMA gives shortlisted bidders (shapefiles, camera locations, CCTV video clips).
- **Format of the evaluation:** ~60-minute live demo + Q&A, plus a POC Walkthrough Note (≤ 10 pages) and a short recording. Demos that rely on generic or pre-recorded marketing content score lower — **everything shown must run on the NTKMA dataset**.
- **The six elements the committee scores (EoI §11.4.2) — every screen must map to one of these:**
  1. Real-time dashboard(s) and unified operating picture for ICCC / leadership
  2. Key operational layers in 2D / 3D
  3. Integration of the provided CCTV footage / sensor data into the twin
  4. Sample analytics outputs (crowd / mobility / environmental)
  5. Alerting mechanism and a sample decision-support workflow
  6. Role-based access / views for different user categories
- **Functional spec:** `docs/FRS_NFR_v0.6.pdf` ("Kumbh DT Portal FRS NFR PoC Review Updates"). Follow its processes P-01 to P-09 and use cases UC-01 to UC-18. Where this file and the FRS disagree, the FRS wins — flag the conflict.

## 2. PoC area

| AOI | Area | Hero features |
|---|---|---|
| AOI-1 | Ramkund / Godavari ghats, Nashik (~1.5 sq km) | Ramkund, ghat steps, Kalaram approach, holding areas, Kalsan / alternate ghats |
| AOI-2 | Trimbakeshwar–Kushavarta (~0.6 sq km) | Trimbakeshwar temple exterior, Kushavarta Kund, darshan queue lanes |

CRS for storage and analysis: **EPSG:32643 (WGS 84 / UTM 43N)**. Serve to the browser in EPSG:4326. Always reproject incoming shapefiles; never assume their CRS — read the `.prj`.

## 3. Users and role-based views (element 6)

| Role | Who | Main screens |
|---|---|---|
| R1 Leadership | Mela Commissioner, senior officers | Zone risk dashboard (UC-11), Overview, Replay |
| R2 Control-room operator | ICCC operator on shift | Live Monitor, Alert detail, AI assistant |
| R3 Zone in-charge | Police / Mela officer of a sector | Zone view, calendar, approvals (diversions) |
| R4 Department officer | Health, Municipal, Traffic desk | Department alert list and tasks |
| R5 Field team | Police, QRT, medical, volunteers | Phone field view (task, route, Accept/Arrived/Done) |
| R6 Help desk | Lost-and-found counter | Missing-person case |
| R7 Planner / GIS analyst | GIS and planning cell | Planning workbench, zones, camera registry |
| R8 Administrator | Platform admin | Rule designer, feed health, users |

For the PoC: login with a role picker (demo users r1…r8). Each role sees only its menu and its alerts.

## 4. One-line system flow (from FRS §1.3)

Cameras and feeds → placed on the map → turned into density, risk and forecasts → rules raise alerts → each alert becomes a task for a named team → the system confirms the area is back to normal → everything is logged for replay.

## 5. Use cases we show (build time: 10 days)

**Shown live in the demo (12):**
- Crowd safety at Ramkund: UC-02 ghat capacity · live crowd density and heatmap (P-03) · UC-06 peak-day overcrowding and diversion (7-step flow: Detect → Map → Predict → Reroute → Regulate → Respond → Restore) · UC-05 one baseline simulation run
- Emergencies and alerts: UC-08 fall at ghat · UC-12 VA alerts on the map · UC-07 missing child (sample search results) · UC-09 Trimbakeshwar queue twin
- Leadership and planning: UC-11 Commissioner's risk dashboard · UC-13 traffic and parking (simulated feed) · UC-03 camera coverage · UC-14 replay and debrief
- Shared processes behind them: P-01 to P-09

**Roadmap only (mention, do not build):** UC-01, UC-04, UC-10, UC-15, UC-16, UC-17, AI assistant, UC-18 voice avatar.
Out of scope: new mobile app, camera control, identity inference, full Mela area.

### Analytics we compute (our code)
| Analytics | Method | Result | User |
|---|---|---|---|
| Zone density, heatmap | count ÷ calibrated camera area; 2 m grid; 30 s | p/m², band, coverage % | ICCC operator |
| Ghat capacity | area × safe density; bathing front × 60 ÷ dip time | capacity, % used | Planner, zone in-charge |
| Alert rules | band; fast rise ≥ 50 %/10 min; bottleneck; 60 s merge | alerts with owner | Operator, departments |
| Crowd forecast | trend projection +30/60/90 min | "critical in N min" | Operator, Commissioner |
| Routing, nearest resource | pgRouting, density-dependent walking speed | route, ETA, nearest team | Operator, field teams |
| Queue wait | people in queue ÷ people leaving per min | wait, overflow | Zone in-charge |
| Zone risk score | weighted density, traffic, weather, river, incidents | 0–100 with reasons | Commissioner |
| Camera coverage | 3D viewshed, DORI classes | coverage %, blind spots | Planner |
| Crowd simulation | pedestrian model (JuPedSim / Vadere) | bottlenecks, clearance time | Planner |
| Missing-person search area | walk-time area at density-adjusted speed | cameras to search | Help desk |
| Response statistics | median ack / resolve vs SLA | SLA table, PDF | Leadership |

### Data we only integrate and show (other systems produce it)
Crowd counts per camera, fall events, intrusion / loitering / abandoned object / women-safety events, person-search results (all from the VA partner); CCTV clips / snapshots; weather (Open-Meteo); river level (SIM); traffic speed and parking occupancy (SIM); ambulance / vehicle GPS (SIM); VMS / PA messages out. The twin places each on the map, links it to a zone and routes it to the right user — it does not compute these.

## 6. Data status (update this table as data arrives)

| ID | Data | Source | Status | Used by |
|---|---|---|---|---|
| N-1 | PoC brief note (location, scope, rules of the demo) | NTKMA | **Expected next week** | Everything — re-read before each session once received |
| N-2 | Shapefiles (zones / ghats / roads / layout — content to be confirmed) | NTKMA | **Expected next week** | P-01, P-08, UC-02, UC-06 |
| N-3 | Camera locations (ID, lat/lon; ask for height, azimuth, FoV) | NTKMA | **Expected next week** | P-09, P-03, UC-03, UC-12 |
| N-4 | CCTV video clips | NTKMA | **Expected next week** | VA → counts/events (see §8), evidence snapshots, AI assistant |
| D-01 | Satellite imagery | Sentinel-2 (public) until NTKMA ortho | Public | Base map |
| D-02 | DEM | Copernicus GLO-30 / SRTM | Public | 3D terrain, slope, viewshed |
| D-03 | Building footprints | Google Open Buildings / MS / OSM, heights estimated | Public + SIM | 3D, viewshed |
| D-04 | Road / walking network | OSM + digitised widths, one-way | Public + SIM | P-08 routing |
| D-06 | River line, ghats, steps | OSM + digitised; step levels assumed | Public + SIM | P-01, UC-02, water-level layer |
| D-07 | Temporary structures (holding areas, parking, toilets, posts) | Indicative placement | SIM | UC-02, UC-06 |
| D-08 | Zones with capacity and safe density | Drawn; sample limits | SIM until NTKMA | Everything |
| D-10 | River water level | Simulated hydrograph | SIM | Water-level layer, UC-11 |
| D-12 | Response assets (medical posts, police posts, PA, VMS) | OSM POIs + placed | Public + SIM | UC-06, UC-08 |
| D-14/15 | Crowd counts, VA alerts | From NTKMA videos via VA (§8), else simulator | SIM / replay | P-03, UC-06, UC-08, UC-12 |
| D-17/18 | Traffic, parking | Simulator | SIM | UC-13 |
| D-20 | Ambulance GPS, emergency calls | Simulator | SIM | UC-08 |
| D-21 | Weather | Open-Meteo | Public | UC-11 |
| D-22 | Ritual calendar | Published Amrit Snan dates (2 Aug, 31 Aug, 11–12 Sep 2027) | Public | UC-10, forecast |

Rule: **anything simulated carries a visible "SIM" label on every screen where it appears** (FRS P-06). No historical Kumbh dataset exists (EoI corrigendum); baselines are synthetic.

### When the NTKMA data arrives — intake steps
1. Copy raw files to `data/raw/ntkma/<date>/` — never edit raw files.
2. For each shapefile: report CRS, geometry type, feature count, attribute columns and 3 sample rows. Do not load before this report is reviewed.
3. Load to PostGIS with `ogr2ogr` into schema `ntkma_raw`, then build clean tables in schema `twin` (reprojected to 32643, valid geometry, our column names).
4. Camera list: map NTKMA IDs to `camera_id` = the video-system ID. Record which fields are missing (height, azimuth, tilt, FoV) — these go back to NTKMA as a query.
5. Videos: inventory each clip (camera ID, duration, resolution, fps, day/night, view type: overview / queue / gate). Match each clip to a camera location.
6. Update the table above and `docs/data_queries.md` (open questions for NTKMA).

## 7. Architecture for the PoC

Keep it light enough to build and run on one laptop, but shaped like the proposed production stack (Angular, Spring Boot, GeoServer, PostGIS, RabbitMQ, Keycloak) so the story is consistent.

```
/config/site.yaml        AOIs, zone types, density bands, thresholds, escalation timers, SIM flags
/data/raw/               untouched inputs
/data/processed/         clean GeoJSON / GeoPackage per layer
/db/                     PostGIS + pgRouting schema, migrations, seed
/sim/                    feed simulator (crowd, VA events, traffic, parking, GPS, gauge) — same JSON as real feeds
/va/                     video → counts/events pipeline (see §8)
/backend/                FastAPI: ingest, density (P-03), rules + alerts (P-02/P-04), forecast (P-07), routing (P-08), WebSocket
/frontend/               CesiumJS (2D + 3D) + dashboard; role-based menus
/frontend/field/         mobile-browser field view (P-05)
/scenarios/              demo timelines (JSON) played by the scenario player
/docs/                   FRS, walkthrough note drafts, data queries
docker-compose.yml       postgis, backend, frontend
```

- Time series: plain Postgres table `zone_density(zone_id, ts, people, density, band, coverage_pct, is_sim)`; TimescaleDB optional.
- Live updates: WebSocket from backend, 30 s refresh (FRS P-03).
- Store numbers and positions only — **never store faces or identities** (FRS P-03, UC-07).
- Configuration over code: thresholds, bands, weights, playbooks and routing tables live in `config/` or DB tables editable from the admin screen (FRS P-02: change a threshold in < 5 min without code).

## 8. Feed contract (VA partner → twin)

The CV partner (Metafusion suggested) or our local pipeline sends JSON messages. The simulator emits **exactly the same format**, so swapping SIM → real changes only the source.

```json
{"msg":"crowd","camera_id":"NSK-CAM-012","ts":"2027-08-02T05:30:00+05:30","count":1320,"density":null,"direction_deg":210,"is_sim":false}
{"msg":"line_cross","camera_id":"NSK-CAM-014","line_id":"G-02-IN","ts":"...","in":42,"out":35,"interval_s":60}
{"msg":"event","camera_id":"NSK-CAM-012","ts":"...","type":"fall","confidence":0.87,"foot_px":[812,640],"snapshot":"snap/abc.jpg"}
{"msg":"event","camera_id":"NSK-CAM-020","ts":"...","type":"intrusion","zone_ref":"DANGER-RK-01"}
```
Event types for UC-12 (at least six): crowd_high, fall, intrusion (river-edge danger strip), loitering, abandoned_object, women_safety, vehicle.

**Turning the NTKMA videos into data (for the demo):**
1. Preferred: give the clips to the CV partner, receive JSON in the contract above.
2. Fallback (our own, label as "AMNEX VA – PoC"): person detection + counting per frame on the clip (e.g., an open-weight detector), line crossing for gate clips, emit the contract at 30 s intervals. Dense-crowd accuracy will be limited — say so on screen.
3. Replay mode: the backend replays the JSON alongside the video in sync, so the demo shows video + map + numbers moving together. This is how we satisfy element 3 "integration of the provided CCTV footage".

## 9. Key calculations (from FRS — implement exactly)

- **Monitored area (P-09):** ground projection of the VA counting ROI ∩ walkable area ∩ effective counting range. If only camera position is available: FoV wedge (azimuth, FoV, range) clipped to walkable area, labelled "approximate". **Never use the planning viewshed as the density denominator.**
- **Density (P-03):** count ÷ monitored area (m²); 2 m grid; overlaps counted once; zone people = Σ density × area over the union of monitored areas; coverage < 50 % → "low confidence"; no data for two cycles → grey "stale".
- **Bands (one set on every screen):** Normal < 2.0 p/m² (green) · Moderate 2.0–3.0 (amber) · High 3.0–4.0 (orange) · Critical ≥ 4.0 (red). Sample values until NTKMA/Police set them.
- **Capacity (UC-02):** waiting area × safe density (flat) + usable step area × safe density (steps) + bathing-front area × safe density (bathing front). Throughput/hour = bathing-front capacity × 60 ÷ dip time (min).
- **Queue wait (UC-09):** wait = people in queue ÷ people leaving per minute (Little's law), per segment.
- **Rules (P-02):** CR-01 density band; CR-02 fast rise ≥ +50 % in 10 min lasting 2 min, only when ≥ Moderate; CR-03 bottleneck from direction; duplicate events from one camera merged within 60 s.
- **Escalation:** High — acknowledge 2 min / resolve 15 min; Medium 5 / 30; Low 15 / 120. Alert closes after 15 min at Normal.
- **Forecast (P-07):** +30/+60/+90 min; trend projection until the LightGBM model (trained on UC-05 simulation runs) passes the validation gate; label which one is shown.
- **Routing (P-08):** pgRouting; walking speed by density (Weidmann: ≈1.34 m/s free, ≈0.33 at 3 p/m², ≈0.16 at 4 p/m²); slower on steps.
- **Risk score (UC-11):** Σ weight × factor (0–100) over density, traffic, weather, river, incidents; equal weights until NTKMA sets them.

## 10. 10-day build plan (one day = one or two Claude Code sessions)

| Day | Build |
|---|---|
| 1 | Repo skeleton, docker-compose, PostGIS + pgRouting, config; base map and 3D for AOI-1 and AOI-2 (public data); scenario player skeleton |
| 2 | NTKMA intake: shapefiles → zones, walkable area (P-01); camera list → registry and camera areas (P-09) |
| 3 | NTKMA clips → VA counts and events (§8); simulator in the same format |
| 4 | P-03 density + heatmap + zone colours; UC-02 capacity; P-06 feed health with SIM / stale labels |
| 5 | P-02 rules, P-04 alert → playbook → task, P-05 field phone view; P-08 routing and nearest resource |
| 6 | UC-06 full 7-step flow incl. forecast (P-07 trend), simulate options, diversion, gate, auto-close |
| 7 | UC-08 fall workflow; UC-12 VA alerts on map; UC-07 with sample search results |
| 8 | UC-09 Trimbakeshwar queues; UC-11 risk dashboard; UC-13 traffic and parking layer |
| 9 | UC-14 replay + PDF; UC-03 coverage; one UC-05 run; role-based views (element 6) |
| 10 | Full 60-min dry run on NTKMA data; POC Walkthrough Note; demo recording |

If NTKMA data is late, Days 1–2 run on public data and Day 2 intake moves to the day it arrives. After each day: run for AOI-1 **and** AOI-2, confirm nothing is hard-coded, update `docs/demo_checklist.md`.

## 11. Demo script (60 min) — the scenario player must be able to run this end to end

| Min | Scene | Element |
|---|---|---|
| 0–5 | Login as R1; unified operating picture of both AOIs; 2D ↔ 3D | 1, 2, 6 |
| 5–12 | R7: zones, walkable area, camera registry with NTKMA cameras; capacity per ghat for Amrit Snan day | 2, 4 |
| 12–20 | NTKMA video playing beside the map; counts flowing into Ramkund heatmap every 30 s | 3, 4 |
| 20–35 | UC-06: fast rise at Ramkund → alert → forecast "critical in 12 min" → simulate options → R3 approves diversion to alternate ghat → gate G-02 regulated, holding area opened → tasks to field phones → back to Normal → auto-close | 4, 5, 6 |
| 35–42 | UC-08: fall detected on ghat steps → nearest medical post + ambulance → corridor → field team closes on phone | 3, 5 |
| 42–48 | UC-09: Trimbakeshwar queue segments, wait time, overflow alert | 4, 5 |
| 48–53 | UC-11 Commissioner dashboard; UC-12 multi-type VA alerts routed to departments | 1, 5, 6 |
| 53–58 | UC-14 replay of the morning + PDF debrief; feed health with SIM labels | 1, 4 |
| 58–60 | Wrap-up: what scales to the full Mela | — |

## 12. Working rules for Claude Code

- Start each module in plan mode; list files to be created/changed before writing code.
- Read `docs/FRS_NFR_v0.6.pdf` section for the module being built; quote the acceptance criteria in the PR/commit message.
- No site-specific values in code — use `config/` and DB.
- Every simulated value carries `is_sim=true` and shows a SIM badge.
- Never store images of people or identities; snapshots only as evidence links for alerts.
- Write a short test for every calculation in §9 with a hand-computed example.
- Keep a running `docs/data_queries.md` of questions for NTKMA (missing camera fields, safe density values, zone owners, SOPs).

## 13. First prompt to paste in Claude Code

> Read CLAUDE.md fully. Then, in plan mode, propose the repo skeleton from §7 and the steps for Day 1 of the 10-day plan (§10) for AOI-1 and AOI-2 using public data only (NTKMA data arrives next week). List what you will download or digitise, the PostGIS schema for zones, cameras, walkable area and zone_density, and how CesiumJS will load the layers. Do not write code until I approve the plan.
