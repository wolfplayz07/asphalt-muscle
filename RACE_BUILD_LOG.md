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
