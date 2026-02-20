# Changelog

## [0.1.5] - 2026-02-20

### Added: Hand-painted CRPG art assets and furnished Inn interior

- Cropped and integrated art from Flux.1-generated concept sheets into individual game assets.
- **New warrior sprites**: idle, back, walk, attack poses + blue tabard variant.
- **New furniture**: table, chairs, bar counter, barrel, keg, shelf, fireplace, torches, rug, candle, lantern, stairs, bar stools, mug, bottle, doors.
- **New wall variants**: standard, stone, fireplace alcove, window.
- **Tiled floor**: dark oak planks tiled to 1024x640.
- **Title background**: medieval village painting.
- Composed Inn interior scene with furniture placement: common room (table + chairs + rug), bar area (counter + keg + shelf + stools), fireplace wall, stairs to upper floor, entrance door.
- Updated `.gitignore` to exclude raw concept art source sheets.

---
## [0.1.4] - 2026-02-20

### Fixed: Inn scene asset rendering and texture references

- Regenerated placeholder art assets with proper dimensions and dark-CRPG styling for title/inn readability.
- Added `assets/wall_gray_v.png` and wired side walls to a vertical wall texture.
- Normalized Inn sprite `image` references in `game.json` to resource names (fixes GDevelop missing-texture placeholder rendering).
- Updated Inn camera target to improve initial room framing.

---
## [0.1.3] - 2026-02-20

### Fixed: JSON-only compatibility and event repair

- Replaced invalid/missing Start scene mouse condition instruction `SourisBoutonRelache` with `MouseButtonReleased`.
- Fixed Inn scene camera action parameter mismatch and removed invalid coordinate-as-object usage that caused diagnostics (`Missing objects: 512`).
- Repaired project open failure by rewriting `game.json` as UTF-8 **without BOM** after edits (strict JSON parser compatibility).
- Verified `game.json` parses cleanly after changes.

---
## [0.1.2] - 2026-02-20

### Fixed: QA review corrections (see QA_REVIEW.md)

- **Mouse condition corrected**: Start scene event now uses `SourisBoutonRelache` (mouse button released) instead of `SourisBouton` (mouse button held down), matching the spec "On Mouse released" and preventing accidental navigation on drag.
- **playableDevices populated**: Changed from empty array to `["desktop", "mobile"]`. GDevelop's built-in touch-to-mouse mapping means the existing `SourisBoutonRelache` + `SourisSurObjet` conditions work for both platforms without extra touch events.

---

## [0.1.1] - 2026-02-20

### Fixed: Complete game.json rewrite to valid GDevelop 5 format

The hand-crafted `game.json` was rejected by GDevelop 5 with "Unable to open the project" because it used an approximate/incorrect internal structure. The file has been completely rewritten to match GDevelop 5's actual serialization format, verified against official test fixtures from the GDevelop GitHub repository (`4ian/GDevelop`).

#### Root Cause
The original file was authored manually with a simplified JSON schema that did not conform to GDevelop 5's internal data model. GDevelop's project loader could not deserialize it.

#### Structural Changes

**Platform Configuration (CRITICAL ÃƒÂ¢Ã¢â€šÂ¬Ã¢â‚¬Â caused the load failure)**
- Added `"platforms": [{"name": "GDevelop JS platform"}]` to `properties`
- Added `"currentPlatform": "GDevelop JS platform"` to `properties`
- Without these fields, GDevelop has no platform to initialize and refuses to open the project

**Top-Level Schema**
- Moved `"firstLayout": "Start"` from inside `properties` to the root level (where GDevelop expects it)
- Added required root-level arrays: `"objectsGroups"`, `"variables"`, `"externalSourceFiles"`
- Added `"resourceFolders": []` to the `resources` block
- Added full `properties` metadata: `adaptGameResolutionAtRuntime`, `folderProject`, `scaleMode`, `sizeOnStartupMode`, `maxFPS`, `minFPS`, `verticalSync`, `loadingScreen` (with progress bar settings), `authorIds`, `authorUsernames`, `categories`, `playableDevices`, `extensionProperties`
- Replaced invalid `projectUuid` (`"crpg-gdevelop-minimal-0001"`) with proper UUID format

**Sprite Object Format**
- Changed animation structure from the invalid `animations > frames` shorthand to the correct `animations > directions > sprites` hierarchy
- Each sprite entry now includes required sub-objects: `originPoint` (`{name: "origine", x, y}`), `centerPoint` (`{automatic: true, name: "centre", x, y}`), `customCollisionMask`, `hasCustomCollisionMask`, `points`
- Added `useMultipleDirections`, `looping`, `timeBetweenFrames` to direction entries
- Added required object-level fields: `variables: []`, `effects: []`, `behaviors: []`, `tags: ""`, `updateIfNotVisible`

**TextObject Format**
- Renamed `"fontSize"` to `"characterSize"` (GDevelop's actual field name)
- Added `"font": ""` (uses default font)
- Added required arrays: `variables`, `effects`, `behaviors`
- Added `"tags": ""`
- Kept `"color": {"r": 255, "g": 255, "b": 255}` object format (confirmed correct from GDevelop reference)

**Instance Format**
- Renamed `"objectName"` to `"name"` on all instances (GDevelop uses `name` to reference the object type)
- Removed invalid `"tint"` property (not a real GDevelop instance field)
- Removed invalid `"centered": true` property (not a real GDevelop instance field)
- Removed `"hidden"` property (not present in GDevelop instance schema)
- Added required arrays: `"numberProperties": []`, `"stringProperties": []`, `"initialVariables": []`
- Adjusted TitleText x from 512 to ~350 and NewGameText x from 512 to ~430 (manual centering since `centered` property doesn't exist)

**Event/Instruction Format**
- Changed condition/action `"type"` from a flat string (`"type": "SourisBoutonRelache"`) to the correct object format: `"type": {"inverted": false, "value": "SourisBoutonRelache"}`
- Added `"subInstructions": []` to all conditions and actions
- Added `"disabled": false` and `"folded": false` to all event blocks
- Fixed `SceneBegin` condition to use correct internal name `"DepartScene"`
- Fixed `SceneChange` action to use correct internal name `"Scene"` with properly quoted scene name parameter: `["", "\"Inn\"", ""]`
- Changed mouse click condition from `"SourisBoutonRelache"` to `"SourisBouton"` (mouse button pressed, more responsive)

**Layout/Scene Format**
- Added required scene background color fields: `"r"`, `"v"` (vert/green), `"b"` (bleu/blue)
- Added `"mangledName"` for each layout (URL-safe version of scene name)
- Added `"disableInputWhenNotFocused"`, `"standardSortMethod"`, `"stopSoundsOnStartup"`, `"title"`
- Added `"objectsGroups": []`, `"variables": []`, `"behaviorsSharedData": []`
- Expanded `uiSettings` to include `gridType`, `gridOffsetX`, `gridOffsetY`, `gridColor`, `gridAlpha`, `zoomFactor`, `windowMask`

**Layer Format**
- Replaced minimal `{"name": ""}` with full layer structure including:
  - `ambientLightColorR/G/B`, `followBaseLayerCamera`, `isLightingLayer`, `isLocked`
  - `renderingType`, `visibility`
  - Full `cameras` array with `defaultSize`, `defaultViewport`, viewport bounds
  - `effects: []`

**Coloring Approach**
- Since `"tint"` is not a valid GDevelop instance property, sprite colors are now applied via `ChangeColor` events at scene start (`DepartScene` condition)
- Start scene: DarkBG colored to `"13;13;20"` (dark navy, #0D0D14)
- Inn scene: Floor `"90;71;54"`, Walls `"128;131;140"`, Bedroom `"64;53;43"`, CommonRoom `"76;62;49"`, WarriorBody `"47;128;255"`

**Resource Entry**
- Added `"alwaysLoaded": false` and `"smoothed": true` fields
- Path confirmed as `"assets/white.png"` (relative to game.json)

#### Files Changed
- `game.json` ÃƒÂ¢Ã¢â€šÂ¬Ã¢â‚¬Â Complete rewrite (5,632 bytes ÃƒÂ¢Ã¢â‚¬Â Ã¢â‚¬â„¢ 28,099 bytes)
- `game.json.bak` ÃƒÂ¢Ã¢â€šÂ¬Ã¢â‚¬Â Backup of previous version (will not be committed)
- `CHANGELOG.md` ÃƒÂ¢Ã¢â€šÂ¬Ã¢â‚¬Â Created

---

## [0.1.0] - 2026-02-20

### Added
- Initial GDevelop 5 project with Start and Inn scenes
- 1x1 white.png placeholder asset
- Project documentation (README.md, SCENES.md, EXPORT.md)
