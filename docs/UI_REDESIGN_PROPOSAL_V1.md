# Grove — UI Redesign Proposal V1

> Applying Anthropic's frontend design skill principles to break out of generic "AI slop" aesthetics
> Date: 2026-02-18

---

## The Problem

Grove currently uses a **safe, generic dark theme** — the exact "distributional convergence" that Anthropic's blog describes:

- **Inter font** everywhere (the #1 "AI default" font)
- **Blue accent (#3b82f6)** — Tailwind's `blue-500`, the most common AI-generated accent color
- **Flat zinc/gray surfaces** with no depth or atmosphere
- **No motion** — static screens with no transitions or micro-interactions
- **No typographic personality** — uniform weight, minimal size contrast

It works. It's clean. But it looks like every other developer tool. **Grove should feel like a place, not a spreadsheet.**

---

## Design Direction: "Deep Forest Terminal"

Grove = a grove of trees. Lean into the metaphor. The app should feel like a **calm, atmospheric workspace** — dark and focused like a late-night coding session, but with organic warmth that says "this isn't just another dev tool."

**Mood:** Late night in a cabin, terminal open, trees outside the window.

---

## 1. Typography Overhaul

### Current
```
Primary: Inter (400/500/600)
Mono: system monospace
Size range: 11px–32px (modest jumps)
```

### Proposed
```
Primary: Space Grotesk → replace with "Bricolage Grotesque" (distinctive, geometric, modern)
  - Or "Outfit" (already in ASCaribe brand — could unify)
Mono: JetBrains Mono (developer-grade, ligatures, personality)
Serif accent: Newsreader (for empty states, onboarding, commit messages)
```

### Key Changes
| Element | Current | Proposed |
|---------|---------|----------|
| Repo names | Inter 600 / 15px | Bricolage Grotesque 700 / 18px |
| File names | Inter 400 / 13px | Bricolage Grotesque 500 / 14px |
| Code/paths | system mono / 12px | JetBrains Mono 400 / 13px |
| Commit messages | Inter 400 / 13px | Newsreader 400 italic / 14px |
| Section headers | Inter 600 / 14px | Bricolage Grotesque 800 / 11px uppercase tracked |
| Empty states | Inter 400 / 14px | Newsreader 300 / 20px |

**Principle:** Use weight extremes (300 vs 800), not timid mid-weights. Size jumps of 2-3x between hierarchy levels.

---

## 2. Color System: Forest Palette

### Current
```
bg:      #09090b (pure black-zinc)
surface: #18181b (zinc-900)
accent:  #3b82f6 (Tailwind blue-500)
green:   #22c55e (Tailwind green-500)
```

### Proposed
```css
/* Deep forest base — warm blacks instead of cold zinc */
--bg:           #0c0f0a;     /* near-black with green undertone */
--surface:      #161b14;     /* dark bark */
--surface-2:    #1e2a1a;     /* moss layer */
--surface-glow: #0d1f0d;     /* subtle green glow for hover states */

/* Accent: Forest teal instead of generic blue */
--accent:       #2dd4a8;     /* mint/teal — distinctive, not blue */
--accent-soft:  rgba(45,212,168,0.10);
--accent-hover: #5eead4;     /* lighter teal for hover */

/* Warm secondary */
--amber:        #fbbf24;     /* sunlight through trees */
--amber-soft:   rgba(251,191,36,0.10);

/* Keep functional colors, warm them up */
--green:        #4ade80;     /* brighter, more alive */
--red:          #f87171;     /* softer red, less alarming */

/* Text: cream instead of pure white */
--text:         #e8e4d9;     /* warm parchment white */
--text-2:       #9ca38c;     /* sage gray */
--text-3:       #5c6354;     /* muted forest */
```

**Why:** The current zinc palette could be any app. Warm blacks with green undertones immediately communicate "Grove" without being heavy-handed. Teal accent is distinctive without being distracting.

---

## 3. Depth & Atmosphere

### Current
Flat surfaces. No gradients. No texture. No shadow.

### Proposed

**A. Subtle background grain**
```css
/* Noise texture overlay on bg — adds organic feel */
background-image: url('data:image/svg+xml,...'); /* subtle grain SVG */
opacity: 0.03;
```
In React Native: Use a very subtle `ImageBackground` with a noise pattern at 3% opacity.

**B. Surface elevation via subtle gradients**
```css
/* Cards/surfaces get a micro-gradient instead of flat color */
background: linear-gradient(180deg, #1a1f17 0%, #161b14 100%);
border: 1px solid rgba(45,212,168,0.06); /* teal-tinted border */
```

**C. Glow effects on interactive elements**
```css
/* Active repo, selected file */
box-shadow: 0 0 20px rgba(45,212,168,0.08);
border-color: rgba(45,212,168,0.2);
```

**D. Header ambient light**
```css
/* Subtle radial gradient behind the header area */
background: radial-gradient(ellipse at top, rgba(45,212,168,0.04) 0%, transparent 60%);
```

---

## 4. Motion & Micro-interactions

### Current
Zero animations. Instant state changes.

### Proposed

**A. Screen transitions**
```tsx
// Shared element transitions for repo → file browser
// Use react-native-reanimated (already installed)
entering={FadeInDown.duration(200).springify()}
```

**B. List stagger on load**
```tsx
// Repos and files animate in with stagger
entering={FadeInDown.delay(index * 50).duration(200)}
```

**C. Pull-to-refresh with personality**
Custom refresh indicator with the Grove tree icon that "grows" as you pull.

**D. Branch switcher**
```tsx
// Dropdown slides down with spring physics
entering={SlideInUp.duration(200).springify().damping(15)}
```

**E. Commit save celebration**
Brief pulse of the teal accent color when a commit succeeds — not a boring Alert.

**F. Drag & drop**
- Dragged item: scale(1.05) + shadow + slight rotation (2°)
- Drop target: pulse glow animation
- On drop: satisfying scale bounce

---

## 5. Component Redesigns

### A. Repository Card (repos.tsx)

**Current:** Flat list item with text
**Proposed:**
```
┌─────────────────────────────────┐
│  ● grove-mvp                    │  ← Bricolage 700, teal dot
│  TypeScript · Updated 2h ago    │  ← JetBrains Mono, text-3
│                                 │
│  Mobile-first GitHub browser    │  ← Newsreader 400 italic, text-2
│                               ▸ │
└─────────────────────────────────┘
  ↑ subtle gradient surface, teal-tinted border on hover, glow on press
```

### B. File Browser Row

**Current:** Icon + filename, flat
**Proposed:**
- Folder icon: filled teal (not outline)
- Files: contextual icons with language color dots
- Selected state: teal left-border accent bar (4px)
- Long-press drag: item lifts with shadow + scale, slight rotation

### C. Commit Row

**Current:** Dot + message + author
**Proposed:**
```
┌─────────────────────────────────┐
│  ┃  fix: handle edge case       │  ← teal bar = human
│  ┃  alimazid · 2h ago           │
│  ┃  +12 -3                      │  ← green/red chips
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  ┃  docs: update README         │  ← amber bar = bot/agent
│  ┃  grove-app[bot] · 5h ago     │
│  ┃  +45 -0                      │
└─────────────────────────────────┘
```
Human vs agent commits distinguished by accent bar color (teal vs amber), not just dot style.

### D. Document Editor

**Current:** TextInput + Alerts
**Proposed:**
- Editor background: slightly different shade (`surface-glow`)
- Cursor color: teal
- Toolbar: floating pill with subtle blur backdrop
- Preview toggle: animated slide between edit/preview
- Commit bar: slide-up animation with teal "Commit" button
- Success: brief teal flash on screen edge (not Alert)

### E. Login Screen

**Current:** Button on dark background
**Proposed:**
- Centered Grove tree logo with subtle ambient glow
- "Grove" in Bricolage Grotesque 800 / 48px
- Tagline in Newsreader 300 italic / 18px: *"Your repos, in your hands"*
- GitHub button with glass-morphism effect
- Background: very subtle animated particle drift (like floating leaves/nodes)

### F. Empty States

**Current:** Plain text
**Proposed:**
- Illustration: minimal line-art tree (SVG)
- Message in Newsreader serif: *"Nothing here yet"*
- Warm, inviting tone — not clinical

---

## 6. Toast System (replacing Alerts)

Replace all `Alert.alert()` with a custom toast component:

```
┌──────────────────────────────────┐
│  ✓  Committed: "fix edge case"   │  ← slides down from top
└──────────────────────────────────┘
```

- Slide in from top with spring physics
- Teal accent bar on left
- Auto-dismiss after 2.5s with fade
- Haptic feedback on appear

---

## 7. App Icon Refresh

**Current:** Git tree with nodes on dark bg + blue accent
**Proposed:** Same concept but:
- Replace blue with teal (#2dd4a8)
- Add subtle depth/gradient to the nodes
- Dark green-black background instead of pure black

---

## Implementation Priority

| Phase | Changes | Effort | Impact |
|-------|---------|--------|--------|
| **1** | Color palette swap (theme.ts) | 1h | High — instant personality |
| **2** | Typography (install fonts, update theme) | 2h | High — professional feel |
| **3** | Toast system (replace Alerts) | 2h | Medium — polish |
| **4** | List stagger animations | 1h | Medium — alive feel |
| **5** | Component redesigns (cards, rows) | 3h | High — cohesive |
| **6** | Login screen redesign | 2h | Medium — first impression |
| **7** | Drag & drop motion polish | 2h | Medium — delight |
| **8** | Background atmosphere (grain, glow) | 1h | Low-Medium — subtle depth |

**Total estimate: ~14 hours of work**

---

## Before / After Mental Model

| Dimension | Current (Generic) | Proposed (Grove) |
|-----------|-------------------|------------------|
| Font | Inter | Bricolage Grotesque + JetBrains Mono + Newsreader |
| Accent | Blue (#3b82f6) | Teal (#2dd4a8) |
| Background | Cold zinc black | Warm forest black with green undertone |
| Text | Pure white | Warm parchment cream |
| Surfaces | Flat solid | Micro-gradients with teal-tinted borders |
| Motion | None | Staggered reveals, spring physics, haptics |
| Feedback | Alert.alert() | Slide-in toasts with haptics |
| Identity | "Could be any app" | "This is Grove" |

---

## Open Questions

1. **Light mode?** — The mockup is dark-only. Do we want a light forest theme too? (cream bg, dark green text, etc.)
2. **Custom fonts in React Native** — Need to bundle via `expo-font`. Adds ~100-200KB to bundle. Worth it?
3. **Performance** — Stagger animations on long file lists. Cap at first 20 items?
4. **Brand alignment** — Should Grove's palette relate to ASCaribe's (caribbean teal #14919b)? Current proposal's teal (#2dd4a8) is in the same family.

---

*This proposal applies Anthropic's "frontend design skill" principles: distinctive typography, committed color theme, purposeful motion, and atmospheric depth — adapted for React Native mobile.*
