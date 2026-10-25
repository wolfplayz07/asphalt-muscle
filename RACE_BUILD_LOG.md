# Race Build Log

## 2026-10-20 — Milestone 1 (major)
- One low-poly muscle car, one loop track, tilt + touch steering, lap timer with best lap. First GitHub Pages build.

## 2026-10-20 — Milestone 2 (major)
- Car/track select menu, 3 original muscle cars (Bandit 68, Thunder SS, Ridgeback 70) with distinct handling, second track Canyon Eight, 3 AI opponents, position HUD, 3-lap races with finish screen.

## 2026-10-21 — Milestone 3 (major)
- Open-world free roam (town, highway, countryside, canyon, minimap). Race markers: park 5s to get a join-race prompt. Faster AI on Canyon Eight.

## Milestone 4 — Cash, garage, dealership, upgrades, paint
- Save `am_save` in localStorage (v1: cash, owned cars with upgrade levels/paint/spent, active car). Starts with $10,000 and the Bandit 68; old or broken saves are cleaned up on load.
- Race payouts: 1st $5000 / 2nd $3000 / 3rd $1500 / 4th $500, shown on the finish screen.
- Garage building in town (green marker, minimap square). Stop inside to get "Enter Garage". Garage screen has a rotating 3D preview and tabs for My Cars, Dealership, Upgrades and Paint.
- Dealership: Bandit 68 $12k, Coyote 67 $16k, Ridgeback 70 $24k, Thunder SS $32k, Stinger 69 $42k, Brute 71 $58k.
- Upgrades: Engine / Transmission / Tires / Weight, 3 levels each. Paint is $500 per coat. Selling returns 60% of price plus upgrade spend.
- The car-select menu is replaced by a title screen; the active car is used in the world and in races. sw.js cache is am-v5 and stays network-first.
- Tested with Playwright headless: 25/25 checks pass, 0 console errors. Draw calls: world 12, race 20, garage 4.

## Steering setting (Tilt / Buttons)
- New Steering choice on the title screen, plus a "Steering: X" toggle button on the world and race HUD. The choice is saved in localStorage `am_steer` (defaults to Buttons).
- Tilt mode: steers with DeviceOrientation. iOS permission is requested when Tilt is chosen and on start. The left/right buttons are hidden; GAS/BRAKE stay visible.
- Buttons mode ignores tilt completely and shows the steer buttons.
- If permission is denied, or no sensor data arrives within 1.5 s, it falls back to Buttons and shows a short notice. The keyboard works in both modes. sw.js is now am-v6 and stays network-first.
- Tested with Playwright: 15/15 steering checks plus the full M4 regression pass, 0 console errors.

## M5 — Realistic town + expanded road network
- Buildings: one merged mesh + one 4×4 canvas texture atlas (windows, shopfronts, doors, shingles, corrugated metal, awnings, FUEL band). Types: houses (pitched roofs, gables, doors, AC units), shops (awnings, parapets, roof AC/boxes), apartment & office towers (lobbies, setbacks), warehouses (roll-up doors, vents), gas stations (kiosk, canopy, pillars, pumps). Varied palettes. Seeded stats: 131 houses, 16 shops, 2 apartments, 7 offices, 4 warehouses, 2 gas stations.
- Placement checks a 5×5 sample grid of each footprint against road edges, keeps 30 m clear around race markers and 20 m around the garage; all buildings are solid AABB colliders.
- Roads: town side streets + alleys, paved back roads, 6 dirt shortcuts (brown, ruts, 82% top-speed cap), 3 highway connectors. Lane markings (yellow/white centre dashes, edge lines), crosswalks at town intersections. Minimap shows every road type (dirt dashed).
- roadInfo() → paved/dirt/off; off-road slowdown treats all new roads as road.
- World: 12 draw calls, ~52k triangles. sw.js → am-v7 (network-first).
- Tests: new M5 headless test all PASS (street 52 m/s paved, dirt 42.6 m/s at cap, building collision, no building/road overlap, both markers → race, garage, steering toggle, 0 console errors); M4 regression 26/26, steering 15/15.

## 2026-10-24 — Milestone 5 (medium)
- Steering setting (Tilt or Buttons). Detailed town (houses, shops, towers, warehouses, gas stations) and expanded roads with side streets, alleys, dirt shortcuts, highway connectors.

## M6a — Highway Sprint race
- New red race marker on the east highway (600,-180), same 5s stop-to-join flow; prompt shows the 1st-place prize.
- Point-to-point 3-lane highway sprint (~2.4 mi) against 3 AI, with lane dashes, start and finish gantries, and a HUD showing miles left.
- 24 light traffic cars drawn as one InstancedMesh (1 draw call). They change lanes smoothly and loop. Hitting one slows or pushes you and shows "CRASH!"; the result screen shows your traffic-hit count. AI racers change lanes to get around traffic.
- Payouts: 1st $7,000 · 2nd $4,000 · 3rd $2,000 · 4th $750 (the circuits stay at $5,000/$3,000/$1,500/$500). Restart, Back to world (clears traffic) and the steering toggle all work.
- Draw calls: race 9 (14.5k tris), world 11–12 (~51k tris). sw.js → am-v8.
- Tests: new highway test /tmp/pwm4/h.js all PASS. M4 26/26, M5 and steering 15/15 also pass, with 0 console errors.

## Drag race (Quarter Mile)
- New yellow marker at (0,-380) on the straight N-S highway (5 s stop-to-join); clear of buildings, garage, other markers.
- Dedicated 402 m two-lane strip, finish gantry, grandstands; Christmas tree (pre-stage, stage, 3 amber, green, red per lane) as one InstancedMesh.
- 1 AI rival in right lane (car perf ±5% of yours, RT 0.17–0.37 s). Cars held until green.
- Gas before green = RED LIGHT foul (DQ, $0). RT ≤ 0.200 s = perfect launch (+$1,000, small launch boost).
- Winner by RT+ET. Payouts: win $3,000, loss $800, +$1,000 perfect launch, foul $0.
- Results: RT, ET, trap mph for both cars; Restart / Back to world; steering toggle works. sw.js am-v9 (network-first).
- Draw calls: drag 4 (1.7k tris). Test /tmp/pwm4/d.js all PASS; t/s/w/h all PASS; 0 console errors.

## Drag fixes + Dirt Rally (am-v10)
- Drag tree: bulbs moved to the camera-facing side (they were hidden behind the backing plate), made bigger (r 0.24, 2.6 m board at z=9), MeshBasic unlit colours plus an additive glow sprite per bulb (InstancedMesh, 1 call). Unlit bulbs are dark grey with no glow.
- CUR/LAST/BEST lap HUD hidden in non-lap races (drag, highway sprint, rally) via body.nolap.
- Dust Devil Rally (t5): purple marker at (-470,120) on a countryside dirt road (5 s stop-to-join). Dirt loop with 8 ordered checkpoint gates; the next gate's banner glows green. Crossing a later gate shows "MISSED CHECKPOINT n! Go back", and the finish doesn't count until all gates are passed.
- Time trial against 3 ghost rivals (no collisions). No speed cap on the rally dirt. Velocity lags heading, so the rear slides and countersteer catches it; sliding scrubs speed.
- HUD: CP n/8, TIME, SPLIT (+/- vs best rival at that gate). Results: positions, times, splits. Payouts $6,000 / $3,500 / $1,800 / $600.
- Draw calls: rally 22 (7.6k tris), drag 4. Tests: new r.js, updated d.js; t/s/w/h pass. w.js marker-on-paved check now expects dirt for the rally marker.

## Bigger map (am-v11)
- World 2x per side: BOUND 880 -> 1760 (3520 x 3520 m, 4x area). Town, garage (55,-40) and all 5 markers unchanged (layout seeded from the original roads only, so it is bit-identical to the old build).
- New regions, each one Group with merged ground and instanced props, hidden beyond 520 m:
  - Desert & Dry Lakebed (east): cracked lakebed, mesas, 420 cacti, rocks.
  - Pine Forest (south, +z): 2400 instanced pines, cabins, logs.
  - Coast & Port (west): sea, beach, quay, 4 cranes, ~360 instanced containers, sheds.
- Roads: highway extensions (outer ring, east/west/forest links), port streets, and dirt back roads (lakebed loop, logging roads, beach road). New roads are drawn in 600 m chunks, culled beyond 850 m.
- Minimap: 260 px backing canvas, scale 130/3600, shows regions, sea, lake and the new roads.
- Draw calls/triangles: spawn 12/50.6k, desert 11/59k, forest 10/157k, coast 12/37k.
- sw.js am-v11 (network-first). Tests: new m.js 30/30; t 26, s 15, w 17, h 18, d 23, r 19, all pass; 0 console errors.

## World highway I-17 (am-v12)
- Added a real I-17 divided highway to the open world. It has 3 lanes each way, a concrete median barrier, shoulders, white and yellow edge lines, dashed lane lines and guardrails with posts. There are 3 overpasses with abutment colliders and 4 sign gantries built from one canvas atlas. It replaces the south ring road and adds an on-ramp from the east road.
- I-17 is road for off-road slowdown (SEGS type 'hwy') and is drawn on the minimap.
- The Highway Sprint (t3) now races on the I-17 eastbound carriageway: race pts equal the world HWP. The open world stays visible during the sprint, and the race ground, road, kerbs and trees are skipped. Traffic, AI, the 5 s marker flow, payouts and Back to world are unchanged.
- Fixed z-fighting between the world ground and the region ground: the desert showed green. The fix is polygonOffset on the world ground plane.
- Draw calls / triangles: spawn 14/50.8k, desert 15/62.6k, forest 10/157k, coast 12/37k, highway race 16/66k. Ambient free-roam traffic was skipped.
- Tests: m.js 27/27 (the map entry above wrongly said 30/30), and h, t, s, w, d, r and hs all pass with 0 console errors. sw.js is am-v12.

## New cars + forest LOD (am-v13)
- Added 4 original classic cars, each with its own low-poly silhouette. Each car is still one merged mesh plus 2 front wheels.
  - Kestrel 66: small pony car. $20,000; top 51, acc 24, grip 2.25.
  - Haymaker 70: pickup-style hauler with a cab and a bed. $28,000; top 49, acc 20, grip 1.85.
  - Longshot 69: long-hood fastback coupe. $48,000; top 61, acc 20, grip 1.75.
  - Hammerhead 72: wide-body brute with fender flares, a hood scoop and a wing. $72,000, the new top-end car; top 63, acc 26, grip 1.9.
- Forest performance:
  - Pines are lower-poly: 20 triangles each instead of about 52.
  - Trees use distance LOD. Full pines are drawn within 300 m, 5-triangle cone impostors from 300 to 440 m, and nothing beyond that (the fog hides it). Instances are re-packed when the player moves more than 12 m, so each tier stays a single draw call. The tree layout is unchanged.
  - Forest view went from 157,480 triangles / 10 draw calls to 23,680 / 11.
- Fixed the race HUD (LAP/CUR/LAST/BEST) showing in the garage.
- sw.js is am-v13 (network-first).
- Tests: new nc.js (50 checks) buys, upgrades, paints, drives and runs all 5 race types with each new car, then sells one and checks the save persists. m 27, h 25, t 26, s 15, w 17, d 23, r 19 and hs all pass with 0 console errors.

## 2026-10-24 — Real car bodies (medium milestone)
- Replaced the boxy car builder with procedural muscle-car bodies:
  - Side-profile body shapes extruded with beveled edges, with a hood, cowl, trunk deck and cut-out wheel arches (dark wheel wells).
  - Tinted glass greenhouse with windshield rake, a fastback or notchback rear glass per model, and A/B/C pillars and roof in body color.
  - Chrome bumpers, a grille with chrome trim, round headlights (quad on Bandit, Ridgeback and Brute), taillights and turn signals, side mirrors, exhaust tips, and racing stripes on hood, roof and trunk.
  - Round wheels with a tire, chrome rim and hub. Front wheels still steer.
- Per-model traits: hood scoops (Ridgeback, Thunder, Bandit), a chrome shaker scoop (Brute), a rear wing (Stinger), a pickup bed (Haymaker), a long hood with a blackout panel (Longshot), and a wide body with fender flares, a big scoop and a wing (Hammerhead).
- Performance: about 1.2k–1.5k triangles per car, 1 material. Body geometry is cached per model and paint, so AI cars and garage previews share it, and the wheel geometry is shared by every car.
- Paint, per-car configs, AI cars and garage/dealership previews are unchanged.
- sw.js is am-v14.

## 2026-10-24 — Body finishes: carbon fiber, fiberglass & more (medium milestone, am-v15)
- New "Body finish" section in the garage Paint tab, under the color swatches. It is saved per car in localStorage.
  - Gloss paint: free (default).
  - Matte: $1,500.
  - Metallic flake: $3,000.
  - Fiberglass: $5,000.
  - Carbon accents (carbon hood, roof and trunk panels): $6,000.
  - Carbon fiber (full body): $12,000.
- Every texture is generated on a 256px canvas (twill carbon weave, chopped-strand fiberglass mat, metal flake noise). Textures are mipmapped and cached once. A tiny gradient environment map is generated with PMREM for reflections.
- Car meshes now use 3 material groups: trim with vertex colors, paint, and accent panels. Your car and garage/dealership previews use MeshPhysicalMaterial with clearcoat. AI cars use MeshStandardMaterial. Materials are cached per finish and color, and geometry is cached per model, so there is no per-car shader or texture cost.
- sw.js is am-v15.
