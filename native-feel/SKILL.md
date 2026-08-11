---
name: native-feel
description: >-
  Makes mobile web UIs feel native by fixing hover/tap lag, viewport height,
  safe areas, overscroll, input zoom, and touch gestures. Use when the user
  runs /native-feel, or asks for native mobile feel, touch polish, iOS/Android
  web UX fixes, or "why does this feel wrong on my phone."
disable-model-invocation: true
license: MIT
metadata:
  version: "1.0"
  tags: [mobile, css, touch, viewport, ux]
---

# Native Feel

Apply these fixes when polishing a mobile web app so it feels closer to a native client. Prefer small CSS/meta changes over new dependencies. Always verify on a real phone when possible — desktop Chrome device mode misses many of these.

## Workflow

1. **Scope** — Identify the surface: full app shell, marketing hero, form, carousel/gesture area, or interactive controls.
2. **Audit** — Scan for the problems in the checklist below (hover styles, tap flash, `100vh`, input `font-size`, missing `touch-action`, overscroll, notch clipping, selectable button text, status bar color).
3. **Patch** — Apply the matching solution. Prefer global base styles for app-wide issues; scope gesture/`user-select` rules to the interactive surface.
4. **Verify** — Recheck the touched items. Call out anything that still needs a real-device test.

## Problem → Solution

| Problem | Solution |
| -------------------------------------------- | ------------------------------------------------------------------------- |
| Hover state stuck after tap | Wrap hover styles in `@media (hover: hover) and (pointer: fine)` |
| Gray/blue flash on tap | Kill `-webkit-tap-highlight-color` |
| Layout has wrong height | `100dvh` (app) or `100svh` (hero) |
| Page zooms into input | Input’s font size should be 16px at the minimum |
| Tap feels laggy | Feedback on pointer-down + `touch-action: manipulation` |
| Pull-to-refresh hijacks scroll | `overscroll-behavior: none` on `html, body` |
| Content stops at the notch | `viewport-fit=cover` + `env(safe-area-inset-*)` |
| Long-press selects button text | Add `user-select: none` |
| Carousel scrolls vertically | `touch-action: pan-y` on the gesture surface |
| Status bar color doesn’t match | `theme-color` per color scheme |
| Right in chrome, wrong on phone | Test on real hardware! |

## Apply patterns

### Hover only on real pointers

```css
@media (hover: hover) and (pointer: fine) {
  .card:hover {
    /* hover styles */
  }
}
```

### Kill tap flash + laggy taps

```css
button,
a,
[role="button"] {
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}

/* Show pressed state on pointer-down, not click */
.button:active {
  transform: scale(0.98);
  opacity: 0.9;
}
```

### Viewport height

```css
.app-shell {
  min-height: 100dvh; /* tracks mobile browser chrome */
}

.hero {
  min-height: 100svh; /* stable; won't jump when URL bar shows/hides */
}
```

### Stop input zoom (iOS)

```css
input,
textarea,
select {
  font-size: 16px; /* below 16px triggers focus zoom on iOS Safari */
}
```

### Overscroll / pull-to-refresh

```css
html,
body {
  overscroll-behavior: none;
}
```

### Notch / home indicator

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
```

```css
.header {
  padding-top: env(safe-area-inset-top);
}
.app-shell {
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
  padding-bottom: env(safe-area-inset-bottom);
}
```

### Unselectable controls

```css
button,
[role="button"],
.toolbar {
  user-select: none;
  -webkit-user-select: none;
}
```

### Carousel / horizontal gesture surface

```css
.carousel {
  touch-action: pan-y; /* vertical page scroll; horizontal handled by gesture code */
}
```

### Status bar / theme color

```html
<meta name="theme-color" content="#ffffff" media="(prefers-color-scheme: light)" />
<meta name="theme-color" content="#000000" media="(prefers-color-scheme: dark)" />
```

Match `theme-color` to the visible top chrome of the current route/theme.

## Defaults when scaffolding mobile UI

If building new mobile UI under this skill, bake these into the base stylesheet/layout unless the project already has equivalents:

- `@media (hover: hover) and (pointer: fine)` for all hover affordances
- `-webkit-tap-highlight-color: transparent` and `touch-action: manipulation` on tappable controls
- `100dvh` for app shells; `100svh` for full-bleed heroes
- `font-size: 16px` minimum on form controls
- `overscroll-behavior: none` on `html, body` for app-like shells
- `viewport-fit=cover` + safe-area padding on fixed headers/footers/shells
- `user-select: none` on buttons/toolbars
- `theme-color` for light and dark

## Output

When reporting work:

1. List which problems you addressed and where (file/selector).
2. Note any remaining items that need **real hardware** confirmation.
3. Do not claim "feels native" from desktop emulation alone.

## Checklist

Use during audits. Check each item against the current surface.

- [ ] Hover styles gated with `@media (hover: hover) and (pointer: fine)`
- [ ] `-webkit-tap-highlight-color: transparent` on tappable controls
- [ ] App shell uses `100dvh`; hero/marketing full-bleed uses `100svh`
- [ ] Inputs/textareas/selects are at least `16px` font size
- [ ] Pressed feedback on pointer-down / `:active`, plus `touch-action: manipulation`
- [ ] `overscroll-behavior: none` on `html, body` when pull-to-refresh should not win
- [ ] Viewport has `viewport-fit=cover`; fixed chrome uses `env(safe-area-inset-*)`
- [ ] Buttons/toolbars have `user-select: none` (and `-webkit-user-select: none`)
- [ ] Gesture surfaces (carousels) set `touch-action: pan-y` appropriately
- [ ] `theme-color` set per light/dark (and route chrome if it changes)
- [ ] Verified on a real phone, not only desktop device mode
