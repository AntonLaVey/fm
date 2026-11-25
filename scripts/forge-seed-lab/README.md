# Forge Predictor

Adds a calculator at `#/calculators/forge-predictor` and a standalone page at `forge-seed-lab/index.html` beneath the site's deployment base.

The supported method needs bonus-bearing items. Best results use several consecutive items with two substats, all displayed decimals, one forge level and complete roll order. Mostly bonus-free level-based recovery is experimental .

The calculator reads its game tables from the same `public/parsed_configs` files as the rest of the app, for the game data version selected in the app (`src/components/ForgePredictor/buildModel.ts`), and sends them to the worker with each request. Forge level, ascension, hammers and tech tree bonuses come from the active profile, and a recovered seed is saved to it. It does not contact the game, request credentials or perform real forges. A unique match is unique under the model, not a measured probability of accuracy. Check a fresh in-game holdout roll; fresh in-game ascended captures are still needed for independent validation.

`model-data.json` is only the embedded copy used by the standalone page and the worker tests. `npm run update:forge-seed-lab-data` regenerates it from `public/parsed_configs`, and `test-app-model.mts` fails when it drifts from the app configs.

The integrated calculator is a native React page using the shared profile item and substat components. The recovery and scan engine runs in a dedicated worker. The large level solver loads only for the experimental level method. The standalone offline page remains available for license notices and offline use. Input storage stays in the browser and downloaded exports stay local.

```bash
npm run build:forge-seed-lab
npm run test:forge-seed-lab
npx tsc -b
npx vite build
```

Rebuild and include `public/forge-seed-lab/index.html` whenever changing the source. The Node build requires no Python or additional dependencies. Artwork is from this project's game assets and retains the game's ownership; embedded code and solver license notices remain visible in the lab.

Tests cover full-domain synthetic bonus recovery, inverse word arithmetic, boundary seeds and equivalence with the Helper combat RNG. These are model checks, not live-game certification. Low-level success and compatibility with newer server configurations remain open validation tasks.

Automatic recovery starts after three complete known rolls and a 900 ms typing pause. Edits cancel the active search and invalidate forecasts. Incomplete rows wait for completion or an explicit Unknown marker. Stop cancels the current search; the next edit can trigger another search. Auto-search can be disabled, and the preference is saved with inputs. The ten-roll bar measures readiness, not a probability of seed accuracy.

Automatic and manual recovery can begin with three known rolls. Recommend at least ten consecutive results; some sequences resolve sooner, while others need more evidence. Unknown entries preserve position and do not satisfy the minimum. Exact item names can remain ambiguous because the initial per-age, per-slot item rotation is unknown even for a unique seed.

Version 4.0 adds a chunked target scanner. Range mode accepts any positive safe-integer count; hunt mode has no preset horizon and stops after the requested 1 to 1,000 matches or cancellation. Both keep only the first 1,000 matching rows, with the original indices, and report total scanned rows and matches. Filters are applied in the worker, including minimum age, slot, text and a strict or inclusive substat threshold rounded to the displayed two decimals. Default matching requires all compatible variants; optional possible matches are marked. Impossible targets are checked before scanning. Exact roll numbering is limited to JavaScript safe integers, and the UI describes this.

The word-based scan RNG is regression checked against the BigInt iterator. No growing list of every scanned roll is retained. Export includes the target, scanned horizon, total matches and retained rows; it is not a full export of every scanned item.

The hammer estimate uses the Helper's MIT-licensed `src/utils/forgeItemLevels.ts` model, transpiled to `hammer-model.js`. It uses the bundled sale base of 20 and level scaling. User inputs provide per-slot displayed maximum item levels, sell bonus and free-forge chance; defaults assume no research. The estimate shows fresh-versus-mature expected average sale revenue, not a guaranteed interval or an exact seed payout. It assumes all items sold at unchanged level and bonuses.

Version 5.0 adds a React calculator route, the existing Anvil navigation asset, compact tool tabs, collapsible roll history and per-roll details. Data versions are persisted and exported with input. Legacy imports without model metadata use legacy data. Changing version or ascension invalidates recovered results.

Latest audited tables: 13 ages and 295 normal items. Heroic, Infinite and Eternal require forge ascension 3. Displayed forge caps are 35, 35, 35 and 50 for ascensions 0 through 3. After any forge ascension all item ages get two substats. Equipment damage/health multipliers are 1, 50, 2500 and 125000; these do not multiply secondary percentages or sale prices. Observations from different ascensions must not be mixed. Sources: parsed configuration 2026_11_16_09_55, forgeConfig.ts, ascensionUnlocks.ts and Items.tsx.

Version 6.0 replaces the integrated iframe with native React controls, a keyboard-accessible image picker, compact roll navigation and shared SecondaryStatCard editing. Predictor percentages preserve their original strings instead of using the profile editor's numeric rounding or clamping. See DATA-AUDIT.md for the UI and logic review.
