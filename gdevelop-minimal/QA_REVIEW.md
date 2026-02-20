# QA Review — game.json

**Date:** 2026-02-20  
**File reviewed:** `gdevelop-minimal/game.json`  
**Reference spec:** `SCENES.md`

---

## Summary

The file is structurally sound and opens correctly in GDevelop 5. Two functional bugs were found and fixed (see **Fixed** section). Several informational findings are noted below.

---

## Fixed

### 1. Mouse condition: "pressed" instead of "released"

**Scene:** Start  
**Severity:** Medium — navigation fires on press rather than on release, which can feel unintentional and may cause accidental navigation on drag.

**Spec (SCENES.md):**
> On Mouse released OR touch end AND cursor on NewGameText → Change scene to "Inn"

**Before:**
```json
{ "type": { "inverted": false, "value": "SourisBouton" }, "parameters": ["", "Left"] }
```

`SourisBouton` = button is currently held down.

**After:**
```json
{ "type": { "inverted": false, "value": "SourisBoutonRelache" }, "parameters": ["", "Left"] }
```

`SourisBoutonRelache` = button was just released (matches "On Mouse released").

---

### 2. `playableDevices` is empty

**Severity:** Low — export and store metadata will not advertise any target platform.

**Before:** `"playableDevices": []`  
**After:** `"playableDevices": ["desktop", "mobile"]`

GDevelop automatically maps touch events to mouse events, so the existing `SourisBoutonRelache` + `SourisSurObjet` pair works for both desktop and mobile touch without additional events.

---

## Informational (no action required)

### 3. Touch event is covered by GDevelop's touch-to-mouse mapping

The spec says "OR touch end". GDevelop 5 simulates mouse events from touch by default, so `SourisBoutonRelache` already fires on tap-release on mobile. No separate `HasAnyTouchEnded` event is needed.

### 4. Author and description fields are empty

`"author": ""` and `"description": ""` are placeholder values. These do not affect gameplay but should be filled before any public release.

### 5. Generic package name

`"packageName": "com.example.crpgminimal"` is a placeholder. Should be updated to a real reverse-domain identifier before building for Android/iOS.

### 6. GDevelop engine version set to 5.0.0

`"gdVersion": { "major": 5, "minor": 0, "build": 0, "revision": 0 }` is intentionally pinned to a low version for maximum compatibility. GDevelop will open it without issue but may show an "older project" warning on newer builds.

### 7. Text objects are not explicitly centered

TitleText is placed at `(350, 180)` and NewGameText at `(430, 280)`. GDevelop `TextObject::Text` has no built-in `centered` flag; position is the top-left origin. Both values produce approximate visual centering in the 1024×640 window. For pixel-perfect alignment, use a GDevelop action to center the object on the X axis or switch to `"alignment": "center"` combined with explicit X math.

### 8. Warrior character has no behaviors

`WarriorBody` has `"behaviors": []`. The current scope is a static demo, so this is expected. When movement or collision is added, a `PlatformBehavior` or `TopDownMovement` behavior should be attached here.

---

## Checklist

| # | Finding | Severity | Status |
|---|---------|----------|--------|
| 1 | Mouse condition uses `SourisBouton` (pressed) instead of `SourisBoutonRelache` (released) | Medium | ✅ Fixed |
| 2 | `playableDevices` is empty | Low | ✅ Fixed |
| 3 | Touch handled via mouse simulation | Info | ✅ Acceptable |
| 4 | Empty author / description | Info | 📝 Noted |
| 5 | Generic package name | Info | 📝 Noted |
| 6 | gdVersion pinned to 5.0.0 | Info | 📝 Noted |
| 7 | Text objects not centered with an action | Info | 📝 Noted |
| 8 | WarriorBody has no behaviors | Info | 📝 Noted |
