---
name: craft-design
description: "Universal Anti-AI High-Craft Design Standard for Web, iOS, Android, and Desktop applications. Use whenever building, styling, or scaffolding websites, apps, landing pages, UI components, forms, or animations. Enforces anti-slop visual heuristics, Emil Kowalski motion physics, Taste Skill art direction, thumb-zone ergonomics, zero-friction flow, and multi-state feedback."
---

# Universal Anti-AI High-Craft Design Guide (Web & Native Apps)

Inspired by Moatassem Abdelhaleem (@moatassem.ai) and the anti-slop design movement, this guide establishes mandatory standards for building tactile, trustworthy, and human-crafted digital products across Web, iOS, Android, and Desktop interfaces.

> **Golden Rule**: *"ما تعملش stop لما يشتغل، كمل لحد ما يبقى polished"* (Never stop when it merely works; keep going until it is polished).
>
> **Related Companion Skills**:
> - For async race conditions, Windows path/locking, and database deadlocks, see the [engineering-reliability skill](file:///C:/Users/SPEED/.gemini/config/skills/engineering-reliability/SKILL.md).
> - For RLS, BOLA/IDOR, and serverless pooling security, see the [vibe-security skill](file:///C:/Users/SPEED/.gemini/config/skills/vibe-security/SKILL.md).

---

## 1. Absolute Blacklists (Zero-Tolerance AI Slop)

| AI Cliché (Banned) | High-Craft Alternative (Mandatory) | Platform Context |
| :--- | :--- | :--- |
| **All Unicode Emojis** (`🚀`, `✨`, `💡`, `🔥`, `👍`, `🎉`, etc.) | **Monochrome Vector Icons**: 1.5px/2px crisp strokes or clean typography. | Web, Mobile, Desktop |
| **Purple/Violet AI Gradients** (`#8B5CF6`, `#6366F1`) | **Intentional Monochrome & Neutral Depth**: Rich deep zinc/neutral shades (`#09090b`, `#121214`), subtle mesh glows, or crisp light surfaces. | Web, Mobile, Desktop |
| **Gradient Text Clips** (`bg-clip-text text-transparent`) | **Crisp High-Contrast Typography**: Solid foreground text with strong contrast against background. | Web, Native Apps |
| **Floating Announcement Pills** (`[ Introducing v2.0 ]`) | **Integrated Section Headlines**: Purposeful page titles without floating decorative pills. | Web, Landing Pages |
| **Decorative Step Badges** (`01`, `02`, `03` on cards) | **Typographic Hierarchy**: Distinct weights, sizes, and spacing without arbitrary numbers. | Web, Mobile Cards |
| **Repetitive Bento Box Grids** (3-4 identical cards in a row) | **Asymmetric Editorial Layouts**: Varied card structures, split-pane inspectors, data tables, and structured ledgers. | Dashboards, SaaS |
| **3 Equal Feature Cards with Icon Boxes** | **Hierarchical Content Ledgers**: Varied split-pane, data table, or asymmetrical feature showcases. | Landing Pages |
| **Cards on Cards on Cards** (infinite nested borders) | **Whitespace Breathes**: Surface contrast, subtle elevation shifts, and generous negative space over nested boxes. | All UI Surfaces |
| **Inter Font Monotony Everywhere** | **Deliberate Typographic Pairing**: Crisp grotesque or editorial serif paired with disciplined monospace accents. | Typography |
| **Glassmorphism Overuse** (`backdrop-blur` on content cards) | **Solid Surface Fills & Hairline Borders**: `border-white/5` or `border-zinc-200/50` with subtle inner shadows. | Web, Mobile |
| **Animating from `scale(0)`** (vacuum pop effect) | **Subtle Baseline Scale**: Animate from `scale(0.93)` to `scale(0.95)` up to `scale(1)`. | Modals, Menus |
| **Animating Layout Properties** (`width`, `height`, `top`) | **GPU-Only Transforms**: Animate only `transform` and `opacity` to eliminate layout thrashing and maintain 60fps. | Animations |
| **AI Marketing Jargon** ("Unlock the power of...", "Seamlessly", "Supercharge") | **Blunt Utilitarian Microcopy**: Active verbs under 8 words ("Export CSV", "Enter the code"). | All Copy |
| **Jumping Dynamic Figures** (proportional numbers shifting layout) | **Tabular Numerals**: `font-variant-numeric: tabular-nums` or monospace font stacks for values, timers, counters. | Counters, Data Tables |

---

## 2. Ergonomics & Mobile App UX (iOS & Android)

### A. Thumb-Zone Architecture
On mobile touch devices, the top corners are physical friction points.
- **Bottom-Heavy Controls**: Place primary CTAs, search bars, filters, and navigation controls in the bottom third of the viewport.
- **Bottom Sheets over Modals**: Use draggable bottom sheets (`Sheet` in SwiftUI, `ModalBottomSheet` in Compose, `Vaul` / `@radix-ui` on Web) rather than centered dialog boxes.
- **Touch Target Sizing**: Minimum clickable area of **48x48dp** (Android) / **44x44pt** (iOS) with generous invisible hit slop:
  ```css
  /* Touch target expansion without visual bloat */
  .touch-target {
    position: relative;
  }
  .touch-target::after {
    content: '';
    position: absolute;
    inset: -12px;
  }
  ```

### B. Virtual Keyboard Management
- **Contextual Keyboards**: Explicitly set `inputmode`:
  ```html
  <input type="text" inputmode="numeric" pattern="[0-9]*" autocomplete="one-time-code" />
  <input type="email" inputmode="email" autocomplete="email" />
  <input type="tel" inputmode="tel" autocomplete="tel" />
  ```
- **Auto-Scroll & Safe Area**: Ensure the active input automatically scrolls above the keyboard when focused (`keyboardAvoidingView` or `scroll-margin-top: 120px`).
- **Dismiss on Scroll**: Tap outside or drag gesture on lists must instantly dismiss the keyboard.

---

## 3. Zero-Friction Flow & Form UX

### A. Auto-Submit on Complete Length
When fixed-length inputs (e.g. 6-digit OTP, 4-digit PIN, 2FA code) reach completion, automatically submit without forcing a button click:
```tsx
const handleDigitChange = (index: number, value: string) => {
  const newCode = [...code];
  newCode[index] = value.slice(-1);
  setCode(newCode);

  if (value && index < 5) {
    inputRefs.current[index + 1]?.focus();
  }

  // Auto-submit on final slot
  const fullCode = newCode.join('');
  if (fullCode.length === 6 && !newCode.includes('')) {
    triggerVerification(fullCode);
  }
};
```

### B. Smart Inline Validation Timing
- **No Premature Shaming**: Never mark an input as invalid while the user is actively typing for the first time.
- **Validate on Blur**: Evaluate correctness only after the user leaves the input field.
- **Live Re-Validation**: Once an error has been flagged on blur, switch to live re-validation so the error indicator disappears the instant the input becomes valid.
- **Accessible Error Linking**:
  ```html
  <input aria-invalid="true" aria-describedby="email-error" />
  <span id="email-error" role="alert" class="text-rose-500 text-xs mt-1">Enter a valid email address</span>
  ```

### C. Non-Blocking Error Recovery
- For non-destructive actions (e.g. archiving, deleting a draft, removing an item), **do not block the user with a confirmation modal**. Execute immediately with an **Undo Toast** (3 to 5 second duration).

---

## 4. Perceived Performance & State UX

### A. Zero Layout Shift (CLS = 0)
- **Geometry-Matched Skeletons**: Skeleton shimmer placeholders must match the exact pixel height, border-radius, and padding of the incoming content.
- Never use generic centered spinners that cause layout collapsing or sudden visual jumps when content renders.

### B. Optimistic UI Updates
- Mutate the UI immediately upon user interaction (e.g. toggling a star, favoriting, updating a quantity).
- Sync in the background. If the request fails, revert state gracefully and present an inline error banner or toast with a retry button.

---

## 5. Tactile Physics, Snapping & Haptics

### A. Draggable Rotary & Wheel Pickers
Replace plain flat horizontal sliders with tangible, physical dial pickers:
- **Momentum Physics**: Compute scroll velocity on drag release with exponential decay.
- **Magnetic Snapping**: Snap to the nearest integer/tick mark cleanly.
- **Velocity Motion Blur**:
  ```css
  .wheel-tick {
    transition: filter 150ms ease-out;
  }
  .is-scrolling-fast .wheel-tick {
    filter: blur(1.5px);
  }
  ```

### B. Critically-Damped Springs (Zero Rubber-Band Wobble)
Ban cartoonish/bouncy overshoot springs. Configure critically damped physics:
```ts
// Framer Motion Preset (Snappy, Zero Wobble)
export const criticallyDampedSpring = {
  type: "spring",
  damping: 30,
  stiffness: 300,
  mass: 0.8,
  restDelta: 0.001
};
```

### C. Motion Engineering Rules (Emil Kowalski / animations.dev)
- **The 300ms Rule**: All UI micro-interactions, dropdowns, sheet reveals, and state changes must stay strictly under **300ms** (sweet spot: 150ms to 250ms). Sluggish animations (400ms+) frustrate users and destroy perceived responsiveness.
- **Never Animate from `scale(0)`**: Modals, dropdowns, and tooltips must enter from a subtle baseline: **`scale(0.93)` to `scale(0.95)` scaling up to `scale(1)`**. Never start from zero, which looks like an unnatural vacuum pop.
- **Ease-Out Deceleration**: Entering UI elements must always use `ease-out` or custom cubic-bezier curves (e.g. `cubic-bezier(0.16, 1, 0.3, 1)`) so they decelerate into position. Never use `ease-in` for entering elements.
- **Tactile Button Press**: Every button must compress on active state:
  ```css
  .button-tactile {
    transition: transform 100ms cubic-bezier(0.16, 1, 0.3, 1);
  }
  .button-tactile:active {
    transform: scale(0.97);
  }
  ```
- **Contextual Tooltip Delays**: Initial hover requires a 300ms - 500ms delay to avoid accidental flicker; subsequent adjacent tooltips hovered in the same sequence open **instantly with 0ms delay**.
- **GPU-Only Transforms**: Only animate `transform` and `opacity`. Never animate `width`, `height`, `top`, `left`, `margin`, or `padding` to prevent layout thrashing and maintain 60fps.

### D. Native Haptic Feedback
- **Selection Tick**: Call `navigator.vibrate(5)` on Web, or `UISelectionFeedbackGenerator` on iOS, or `HapticFeedbackConstants.CLOCK_TICK` on Android when passing tick marks.
- **Impact Feedback**: Medium impact when triggering primary progression actions.
- **Error Buzz**: Double micro-vibration (`[10, 50, 10]`) on validation error.

### E. 20 Precision Motion Rules (Concrete Parameters)
1. **Ease-out** for entrances (`250ms`), **ease-in-out** for on-screen moves (`300ms`).
2. **Dropdowns**: `150–250ms` (never `600ms`).
3. **Stagger list rows**: `30ms` between rows; **details/badges trail by `50ms`** as containers settle.
4. **Tactile press**: `scale(0.97)` (`150ms`); **area-preserving knob stretch**: `12%` on toggle/thumb movement.
5. **Arcs & Pulse**: Move objects along subtle `32px` arcs (not straight lines); use a single subtle pulse to confirm success (`Saved`).
6. **Popovers & Origin**: Start popovers at `scale(0.95)`, **enter from & exit toward the trigger** (`transform-origin` at trigger, never center).
7. **Asymmetric Exits & Overlap**: Make exits **~20% shorter** than entrances (`250ms` in / `200ms` out), and **overlap old & new states** (never fade out -> blank beat -> fade in).
8. **Zero Everyday Bounce & Direct Drag**: No underdamped overshoot on everyday UI; **never tween while held** (1:1 direct pointer tracking), and use **diminishing-resistance rubber-banding** past drag bounds.
9. **Masks, Icon Crossfade & Shared `layoutId`**: Use clip/masks for reveals and color shifts; crossfade icon swaps with `scale + opacity + blur`; reuse the **same `layoutId`** across shared objects.

---

## 6. Multi-State Feedback Loop

Every interactive element must explicitly handle 4 distinct states:
1. **Idle**: High-contrast, clean surfaces, borderless or hairline outline.
2. **Interacting**: Active slot border highlight, gentle scale pulse (`scale(1.02)`), active cursor indicator.
3. **Evaluating / Pending**: Integrated loading dots or smooth micro-spinner; no layout jump.
4. **Resolution**:
   - *Success*: Verified checkmark vector morph, soft green accent resolution.
   - *Error*: Micro-shake keyframe animation (`translateX(-4px)` to `+4px`), gentle red outline, error message fade-in.

---

## 7. Taste Skill Art Direction Profiles (tasteskill.dev)

To prevent models from defaulting to generic "category averages" (3-line eyebrow headers, 3 equal feature cards, safe beige neutrals), explicitly select and adhere to one of four high-craft art direction profiles:

1. **Technical Data Ledger**:
   - *Aesthetic*: Dense, utilitarian, keyboard-driven dashboard.
   - *Palette*: Deep zinc surfaces (`#09090b`), slate hairline borders (`#27272a`), single status accent (emerald/amber).
   - *Typography*: Strict monospace tabular numerals (`font-mono`, `tabular-nums`) with compact sans-serif labels.
   - *Layout*: Data tables, split-pane inspectors, and keyboard shortcut guides.

2. **Editorial Monospace**:
   - *Aesthetic*: High-end publications and design-forward SaaS.
   - *Palette*: Paper white (`#fafafa`) or rich onyx (`#0d0d0d`) with neutral borders.
   - *Typography*: High-contrast serif headlines paired with crisp monospace labels and metadata.
   - *Layout*: Asymmetrical grids, generous negative space ("whitespace breathes"), and left-aligned typographic anchors.

3. **Crisp Modernist**:
   - *Aesthetic*: Sleek, responsive engineering platforms (Linear / Vercel style).
   - *Palette*: Monochromatic grayscale with high-contrast foreground text.
   - *Typography*: Tight-tracked geometric grotesque (`tracking-tight`) with uppercase micro-labels.
   - *Layout*: Split-hero with authentic photography, staggered multi-column ledger, and sticky navigation.

4. **Brutalist Minimalist**:
   - *Aesthetic*: Raw, unpretentious developer tools.
   - *Palette*: Pure black and white with high-contrast borders (`border-zinc-900` / `border-zinc-100`).
   - *Typography*: Monospace or raw sans-serif across all elements.
   - *Layout*: Gridlines visible as structural dividers, zero decorative glows, zero floating pills.

### A. Production Motion & Component Stack
Never let AI generate generic SaaS landing pages with flat CSS transitions. For marketing, portfolio, and editorial web builds, equip the project with (`npm install gsap lenis` per project):
- **GSAP (`gsap` — `https://github.com/greensock/GSAP`)**: Industry-standard timeline sequencing and scroll-triggered choreography (`ease: "power4.out"`). Reserve for hero entrances, orchestrated reveals, and scroll-linked storytelling.
- **Lenis (`lenis` — `https://github.com/darkroomengineering/lenis`)**: Smooth, damped scroll inertia that eliminates jerky native wheel jumps and gives long-form pages physical weight. Always disable when `window.matchMedia('(prefers-reduced-motion: reduce)').matches` is true.
- **React Bits (`https://github.com/DavidHDev/react-bits` / `reactbits.dev`) & 21st.dev (`https://21st.dev`)**: Curated creative-developer component patterns (kinetic text, magnetic buttons, interactive canvases, bespoke cards) — adapt their mechanics to your single-accent neutral palette rather than copying purple/neon defaults.

### B. 20 Curated Visual & Animation Styles
When a project calls for a distinctive creative direction (rather than generic SaaS cards), commit cleanly to one cohesive style from this vocabulary while keeping all Anti-AI bans (no purple slop gradients, GPU-only `transform`/`opacity` for UI, `<300ms` micro-interactions):
- **Typographic & Mechanical**: *Kinetic Type* (rhythmic headline motion), *Split-Flap* (departure-board character flips for live counters/status), *ASCII Art* (monospace terminal shaders/grids).
- **Architectural & Editorial**: *Blueprint* (technical CAD gridlines, dimension callouts, monospace specs), *Bauhaus* (primary geometric shapes, asymmetric grid balance), *Art Deco* (hairline gold/brass geometric framing on deep onyx), *Neo-Brutalism* (hard 2px-3px borders, offset solid shadows, zero blur, crisp high-contrast blocks).
- **Tactile & Dimensional**: *Paper Cutout* (layered elevation shadows), *Hand-Drawn* (subtle SVG stroke-dasharray reveals), *Isometric* (3D-projected architectural diagrams), *Clay 3D* (soft matte sculptural forms), *Physics* (matter-based collision/gravity toys for interactive sandboxes).
- **Atmospheric & Retro** *(use sparingly, strictly single-accent, never purple slop)*: *Particles* (subtle canvas starfields/data points), *Liquid Morph* (SVG gooey/metaball transitions), *Neon Glow*, *Y2K Chrome*, *Holographic*, *Retro VHS*, *Comic Book*, *Pixel Art*.

---

## 8. Closed-Loop Visual Verification (Figma MCP + Browser DevTools / Playwright)

Never declare frontend work complete based solely on raw source code. Enforce a closed feedback loop:
1. **Token Alignment**: Check rendered CSS properties against design tokens or Figma specifications.
2. **Multi-Viewport Headless Render**: Render and screenshot the interface across 3 canonical screen sizes:
   - Mobile: 390x844 (iPhone 14/15)
   - Tablet: 768x1024 (iPad)
   - Desktop: 1440x900 (MacBook Pro)
3. **Automated Visual Inspection**:
   - Touch targets verified for thumb reachability and minimum 48x48dp.
   - Contrast ratios verified against WCAG AA standards (4.5:1 text, 3:1 UI components).
   - Horizontal overflow checked (`overflow-x: hidden`).
4. **Iterative Polish**: Refine layout, spacing, and microcopy until visually verified before final handoff.

---

## 9. Universal Craft Audit Checklist

Before declaring any UI, component, or screen complete, run this 12-point audit:

- [ ] **Zero Emojis**: All Unicode emojis purged across UI, copy, buttons, badges, and headers.
- [ ] **Anti-AI Styling**: Purple gradients, gradient text clips, floating pills, and bento-box grids eliminated.
- [ ] **Anti-Category Averages**: 3-line eyebrow headers, 3 equal feature cards, and beige clichés eliminated.
- [ ] **Anti-Nesting (Whitespace Breathes)**: "Cards on cards on cards" replaced by surface elevation and negative space.
- [ ] **Typographic Intent**: Font pairing deliberate without mindless Inter font monotony.
- [ ] **Single Accent**: Exactly ONE primary brand accent color; container surfaces strictly neutral grayscale.
- [ ] **Thumb-Zone Ergonomics**: Mobile touch targets 48x48dp+; primary CTAs anchored to the bottom third.
- [ ] **Zero-Friction**: Auto-submit on completed length, auto-focus on mount, native autofill attributes present.
- [ ] **Smart Validation**: Inline validation deferred to blur; live re-validation upon typing fix.
- [ ] **Layout Stability**: Skeleton loaders match final content geometry ($\text{CLS} = 0$); optimistic updates enabled.
- [ ] **Motion Physics (300ms & Scale)**: Micro-interactions under 300ms, entering from scale(0.95), GPU transforms only.
- [ ] **Visual Verification**: Layout verified via rendered screenshot inspection across mobile and desktop viewports.

---

## 10. Conversion UX & Utility Component Recipes

### A. Sticky Mobile Thumb-Zone CTA
Anchor key progression actions on mobile devices while maintaining safe area padding:
```tsx
export function StickyMobileCTA({ label, price, onClick }: { label: string; price?: string; onClick: () => void }) {
  return (
    <div className="fixed bottom-0 inset-x-0 p-4 bg-zinc-950/95 border-t border-zinc-800/80 backdrop-blur-sm sm:hidden z-50 pb-safe">
      <div className="flex items-center justify-between gap-4 max-w-md mx-auto">
        {price && <span className="text-sm font-mono font-medium text-zinc-200">{price}</span>}
        <button
          onClick={onClick}
          className="flex-1 h-12 bg-zinc-100 hover:bg-white text-zinc-950 font-medium text-sm rounded-lg active:scale-[0.98] transition-transform duration-100 flex items-center justify-center"
        >
          {label}
        </button>
      </div>
    </div>
  );
}
```

### B. Form Draft Autosave (Zero Data Loss)
Prevent users from losing form progress on accidental refresh or back navigation:
```tsx
import { useEffect, useState } from "react";

export function useFormAutosave<T>(storageKey: string, initialValues: T) {
  const [values, setValues] = useState<T>(() => {
    const saved = typeof window !== "undefined" ? localStorage.getItem(storageKey) : null;
    return saved ? JSON.parse(saved) : initialValues;
  });

  useEffect(() => {
    localStorage.setItem(storageKey, JSON.stringify(values));
  }, [storageKey, values]);

  const clearDraft = () => localStorage.removeItem(storageKey);

  return [values, setValues, clearDraft] as const;
}
```

### C. Password Visibility Toggle
Clean, accessible show/hide password control:
```tsx
export function PasswordInput({ value, onChange }: { value: string; onChange: (v: string) => void }) {
  const [visible, setVisible] = useState(false);

  return (
    <div className="relative">
      <input
        type={visible ? "text" : "password"}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        className="w-full h-11 px-3.5 pr-10 bg-zinc-900 border border-zinc-800 rounded-lg text-sm text-zinc-100 focus:outline-none focus:border-zinc-500"
      />
      <button
        type="button"
        onClick={() => setVisible(!visible)}
        aria-label={visible ? "Hide password" : "Show password"}
        className="absolute right-3 top-1/2 -translate-y-1/2 text-zinc-400 hover:text-zinc-200 text-xs font-mono"
      >
        {visible ? "Hide" : "Show"}
      </button>
    </div>
  );
}
```

### D. Copy-to-Clipboard with Micro-Feedback
```tsx
export function CopyButton({ text }: { text: string }) {
  const [copied, setCopied] = useState(false);

  const handleCopy = async () => {
    await navigator.clipboard.writeText(text);
    setCopied(true);
    setTimeout(() => setCopied(false), 2000);
  };

  return (
    <button
      onClick={handleCopy}
      className="inline-flex items-center gap-1.5 px-2.5 py-1 text-xs font-mono rounded bg-zinc-800 hover:bg-zinc-700 text-zinc-300 transition-colors"
    >
      {copied ? "Copied" : "Copy"}
    </button>
  );
}
```

---

## 11. Editorial De-Slop Guide: Before vs. After

| AI Slop Copy (Banned) | High-Craft De-Slop Alternative | Why It Converts Better |
| :--- | :--- | :--- |
| "Revolutionizing the Future of Plumbing" | "Same-Day Emergency Plumber in Sydney's Inner West" | Tells the customer exactly what service is provided and where. |
| "Unlock the Power of Seamless Task Management" | "Lightweight Issue Tracker for 5-Person Dev Teams" | Concrete target customer and actual product definition. |
| "10,000+ Happy Clients Worldwide | 99.9% Satisfaction" | "Trusted by 14 local workshops across New South Wales" | Believable, local, verifiable proof rather than fabricated AI statistics. |
| "Innovative Solutions, Seamless Experience, Next-Level Quality" | "Hot water repairs, blocked drain clearing, and pipe replacements" | Explicit list of actual jobs completed instead of corporate jargon. |

---

## 12. Platform & Runtime Bug Defense Patterns

### A. iOS Mobile Safari Viewport & Input Zoom Armor
```css
/* Prevent iOS Safari 100vh toolbar cut-off */
.full-screen-container {
  min-height: 100vh;
  min-height: 100dvh;
}

/* Prevent iOS Safari automatic auto-zoom on input focus */
@media screen and (max-width: 768px) {
  input, select, textarea {
    font-size: 16px !important;
  }
}

/* Prevent modal background rubber-banding */
.modal-overlay, .drawer-sheet {
  overscroll-behavior: contain;
}
```

### B. Race-Condition-Free Async Search Hook
```tsx
import { useEffect, useState } from "react";

export function useRaceConditionSafeSearch<T>(query: string, fetcher: (q: string, signal: AbortSignal) => Promise<T[]>) {
  const [results, setResults] = useState<T[]>([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      return;
    }

    const controller = new AbortController();
    setLoading(true);

    fetcher(query, controller.signal)
      .then((data) => {
        setResults(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          setLoading(false);
        }
      });

    // Abort active fetch if user types again before this request completes
    return () => controller.abort();
  }, [query]);

  return { results, loading };
}
```

### C. Safe Financial Math (Integer Cents Utility)
```ts
export const CurrencyMath = {
  toCents: (dollars: number): number => Math.round(dollars * 100),
  toDollars: (cents: number): number => cents / 100,
  add: (centsA: number, centsB: number): number => centsA + centsB,
  formatUSD: (cents: number): string => {
    return new Intl.NumberFormat("en-US", {
      style: "currency",
      currency: "USD"
    }).format(cents / 100);
  }
};
```

---

## 13. Color Hierarchy, Anti-Clutter & Design Engineering Vocabulary

### A. The 60 / 30 / 10 Color Rule & Tinted Neutrals
- **60% Primary Surface / Canvas**: Dominant background neutral (`#09090b` or tinted dark/light neutral).
- **30% Secondary Surface / Structure**: Cards, sidebars, and borders using **Tinted Neutrals** (greys with a subtle hue tint or **OKLCH** perceptually uniform lightness) so they feel intentional rather than raw default `#808080`.
- **10% Single Accent — CTA Only**: Reserve the single vibrant accent color **strictly for the primary action button you want pressed** (and active focus/selection). Never scatter accent color across decorative badges or secondary buttons.

### B. Anti-Clutter, Settings Consolidation & Breathing Room
- **Master-Detail Progressive Disclosure (1:1 Specific Routing)**: Never dump full interactive charts, multi-row expandable tables, AQI gauges, or secondary telemetry onto the main overview screen. Keep the main overview screen limited to a focused Hero + clickable KPI summary cards (`Gap` / `Spacing` breathing room), and route **each card 1:1 to its own dedicated scrollable Detail Sub-Page** (with `"<- Overview"` back navigation + top-level tab switcher) where the full interactive graphs, tables, and gauges live. If using a slide-over drawer, enforce `flex-1 min-h-0 overflow-y-auto overscroll-contain`.
- **Gear-Icon Settings Consolidation (Zero Main-Page Leaks)**: Never clutter the main header, sidebar, or workspace with inline settings toggles, labels, or shortcuts (units, theme switchers, refresh buttons). Remove ALL old inline settings UI completely once consolidated into a single **Gear Icon** popover/modal/drawer, and use an unmistakable notched cog/gear SVG icon (never a radial sun-circle icon that mimics a theme toggle).
- **Generous Spacing (Anti-Compaction)**: Maintain clear visual separation and breathing room between sections, cards, and metrics (`gap-6`/`gap-8`, `p-6`, never edge-to-edge compacted grids).

### C. Design Engineering Vocabulary Invariants
- **Typography**: Honor *Kerning* (pair spacing), *Tracking* (letter-spacing on caps/labels), *Leading* (line-height), *x-height* pairing, *Line length* (`max-w-[65ch]` for readable prose), *Tabular numbers* (`tabular-nums`), and eliminate *Widows* & *Orphans* (`text-wrap: balance` on headings, `text-wrap: pretty` on paragraphs).
- **Layout & Ergonomics**: *Optical center* (nudge asymmetric icons like play triangles 1-2px visually center), *Safe area* (`env(safe-area-inset-*)`), *Affordance* (clear interactive cues), *Skeleton screen* (exact-geometry placeholders), *Empty state* (intentional zero-state with primary CTA).
- **Motion & Controls**: *Origin-aware animation* (popovers/menus expand from their trigger coordinates), *Interruptible animation* (animations smoothly retarget mid-flight on rapid clicks), *Shared element transition* (cards morph smoothly into detail views), *Stagger* (sequenced list reveals under 30ms/item), *Ease-out* (`cubic-bezier(0.16, 1, 0.3, 1)`), *Combobox* (filterable text-input + list).

---

## 14. 20 Principles of UX Laws & Motion Craft

### A. 12 Motion Principles Adapted for UI
1. **Squash & Stretch**: Subtle scale compression on impact (`active:scale-[0.97]`) to convey physical weight.
2. **Anticipation Wind-Up**: Tiny micro-pause or counter-nudge before major state transitions.
3. **Staging the Focus**: Direct attention to one element at a time instead of animating everything simultaneously.
4. **Pose to Pose**: Define crisp start/end key poses; let easing interpolate the middle cleanly.
5. **Follow Through**: Secondary inner elements settle slightly after the main container stops.
6. **Slow In & Slow Out**: Never use robotic `linear` timing for UI motion; use `ease-out` / `ease-in-out`.
7. **Moving in Arcs**: Natural curved trajectories for floating/dragging transitions rather than rigid straight lines.
8. **Secondary Action**: Subtle supporting flourish (e.g., checkmark stroke draw) backing up the primary confirmation.
9. **Snappy Timing**: Keep UI micro-interactions under `300ms` (sweet spot `150ms`).
10. **Exaggerated Feedback**: Clear horizontal shake on invalid input so errors are unmistakable.
11. **Solid Drawing in 3D**: Subtle `perspective: 700px` tilt/depth on tactile cards where appropriate.
12. **Overall Appeal**: The sum of every micro-detail executed with care.

### B. 8 Classic UX Laws
13. **Fitts's Law**: Bigger, closer targets are faster and easier to hit.
14. **Bigger Hit Areas**: Expand clickable hit zones (`44x44`+) beyond small `24x24` visual icons.
15. **Hick's Law**: Fewer choices reduce decision time — trim unnecessary options.
16. **Miller's Law**: Humans hold ~7 chunks at once — format long numbers (cards, phones, OTPs) into spaced chunks.
17. **Doherty Threshold**: Respond within `400ms` (ideally `<100ms` optimistically) or the UI feels sluggish.
18. **Postel's Law**: Accept messy user input (e.g., `15 jan`, mixed-case emails, spaces in card numbers) and normalize cleanly.
19. **Jakob's Law**: Respect familiar conventions (users spend most time on other apps and expect standard patterns).
20. **Frequency of Use**: The more frequently an action is triggered (e.g., `Cmd+K` command palette), the less it should animate — make high-frequency controls instant.


