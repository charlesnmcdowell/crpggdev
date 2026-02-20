# Changelog

## [0.1.1] - 2026-02-20

### Fixed: Complete game.json rewrite to valid GDevelop 5 format

The hand-crafted `game.json` was rejected by GDevelop 5 with "Unable to open the project" because it used an approximate/incorrect internal structure. The file has been completely rewritten to match GDevelop 5's actual serialization format, verified against official test fixtures from the GDevelop GitHub repository (`4ian/GDevelop`).

#### Root Cause
The original file was authored manually with a simplified JSON schema that did not conform to GDevelop 5's internal data model. GDevelop's project loader could not deserialize it.

#### Structural Changes

**Platform Configuration (CRITICAL — caused the load failure)**
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
- `game.json` — Complete rewrite (5,632 bytes → 28,099 bytes)
- `game.json.bak` — Backup of previous version (will not be committed)
- `CHANGELOG.md` — Created

---

## [0.1.0] - 2026-02-20

### Added
- Initial GDevelop 5 project with Start and Inn scenes
- 1x1 white.png placeholder asset
- Project documentation (README.md, SCENES.md, EXPORT.md)
