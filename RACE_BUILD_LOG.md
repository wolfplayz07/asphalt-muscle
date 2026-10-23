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
