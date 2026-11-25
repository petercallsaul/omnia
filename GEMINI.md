# Superpowers Bootstrap Rule

<SUBAGENT-STOP>
If you were dispatched as a subagent to execute a specific task, ignore this skill.
</SUBAGENT-STOP>

<EXTREMELY-IMPORTANT>
If you think there is even a 1% chance a skill might apply to what you are doing, you ABSOLUTELY MUST invoke the skill.
IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE. YOU MUST USE IT.
This is not negotiable. You cannot rationalize your way out of this.
</EXTREMELY-IMPORTANT>

## The Rule

**Invoke relevant or requested skills BEFORE any response or action** — including clarifying questions, exploring the codebase, or checking files. If it turns out wrong for the situation, you don't have to use it.
**Before entering plan mode:** invoke the brainstorming skill first.
Then announce "Using [skill] to [purpose]" and follow the skill exactly.

## Skill Priority

When multiple skills apply, process skills come first:
- "Let's build X" -> superpowers:brainstorming first, then implementation skills.
- "Fix this bug" -> superpowers:systematic-debugging first, then domain skills.

## Red Flags

These thoughts mean STOP—you're rationalizing:
| Thought | Reality |
| :--- | :--- |
| "This is just a simple question" | Questions are tasks. Check for skills. |
| "I need more context first" | Skill check comes BEFORE clarifying questions. |
| "Let me explore the codebase first" | Skills tell you HOW to explore. Check first. |
| "I can check git/files quickly" | Files lack conversation context. Check for skills. |
| "Let me gather information first" | Skills tell you HOW to gather information. |
| "This doesn't need a formal skill" | If a skill exists, use it. |
| "The skill is overkill" | Simple things become complex. Use it. |
| "I'll just do this one thing first" | Check BEFORE doing anything. |

## User Instructions

User instructions take precedence over skills, which override default behavior.

## Fullstack Orchestrator Rule (`/fullstack`)

Whenever the user runs `/fullstack` or asks for fullstack orchestration:
1. Never perform piecemeal single-tool actions.
2. Survey workspace and audit ALL installed plugins and skills; strictly exclude tools irrelevant to project domain.
3. For applicable skills, build a 6-stage lifecycle plan and execute systematically.

## Mandatory Skill Invocation Invariants

Whenever asked to create, build, scaffold, design, code, refactor, or audit ANY website, mobile app, desktop app, backend API, or database:
1. **UI/UX & Motion**: Invoke `craft-design` (`./skills/craft-design/SKILL.md`) before writing any frontend code.
2. **Security & Auth**: Invoke `vibe-security` (`./skills/vibe-security/SKILL.md`) before writing any auth, DB, or API code.
3. **Reliability & Bugs**: Invoke `engineering-reliability` (`./skills/engineering-reliability/SKILL.md`) before writing async, mobile, Windows, or query logic.

---

## Anti-AI High-Craft Design Standard (Moatassem.ai Philosophy)

Mandatory across Web, iOS, Android, and Desktop frontend. Never stop when it merely works; keep going until it is polished.

### 1. Absolute Blacklists (Zero Tolerance)
- **100% Emoji Ban**: Forbid ALL Unicode emojis across UI, buttons, navigation, headers, cards, badges, and copy. Use crisp monochrome SVG icons (1.5px/2px stroke) or clean typography.
- **Banned Color Clichés**: No purple/violet/indigo AI gradients (`#8B5CF6`, `#6366F1`, shiny violet button cards). Strictly ONE primary brand accent color; container surfaces strictly neutral grayscale (zinc/slate/neutral).
- **Banned Visual Effects**: No gradient text clips (`bg-clip-text text-transparent`), floating announcement pills (`[ Introducing v2.0 ]`), decorative step badges (`01 / 02 / 03`), neon glow shadows, or frosted glass (`backdrop-blur`) on standard content cards.
- **Banned Copywriting Slop**: Ban AI filler: "Unlock the power of...", "Seamlessly integrate", "Revolutionary platform", "Supercharge your workflow", "Delve into...", "Everything you need to...", fake testimonials, and made-up metrics. Microcopy must be blunt and under 8 words (e.g. "Enter the code", "Export CSV", "Verify phone number").
- **Banned Category Averages (Taste Skill)**: No centered 3-line headline with eyebrow badge, no 3 equal feature cards in a row with small icon boxes, no safe warm-beige neutral palettes, and no hand-rolled generic SVG card grids.
- **Banned Nested Cards**: No "cards on top of cards on top of cards" (infinite nested borders). Whitespace breathes: use surface contrast, elevation shifts, and negative space instead of nesting border boxes.
- **Banned Font Monotony**: Avoid blindly defaulting to Inter font everywhere. Pair deliberate typography (editorial serif, geometric grotesque, or technical monospace). Always enforce `font-variant-numeric: tabular-nums` for dynamic numbers, counters, and timers.
- **Banned Animation Anti-Patterns (Emil Kowalski)**: Never animate popovers/modals from `scale(0)` (vacuum pop); enter from `scale(0.93)`-`scale(0.95)` to `scale(1)`. Never use `ease-in` for entering elements. Restrict animations strictly to GPU-accelerated `transform` and `opacity` (never `width`/`height`/`top`/`left`).
- **60/30/10 & Anti-Clutter**: 60% tinted-neutral canvas, 30% secondary surface, 10% single accent (primary CTAs only). Keep main screens uncluttered: each clickable card routes 1:1 to its own distinct scrollable detail view (`min-h-0 overflow-y-auto`, Lenis paused on modals), purge all old inline settings/toggles/labels from the main page into one notched Cog Gear icon, and use generous spacing.

### 2. Ergonomics & Mobile App UX (iOS & Android)
- **Thumb-Zone Architecture**: Anchor primary actions, bottom sheets, navigation tabs, and key interactive controls to the bottom third of the screen. Never place critical progression CTAs in unreachable top corners.
- **Touch Targets**: Minimum 48x48dp (Android) / 44x44pt (iOS) clickable area with generous invisible hit slop (`padding: 12px`).
- **Native Platform Gestures**: Support edge-swipe to go back (iOS/Android), pull-to-refresh with spring damping, swipe-to-dismiss sheets, and fluid drag handles.
- **Contextual Keyboards**: Specify explicit `inputmode` (`numeric`, `email`, `tel`, `decimal`, `url`). Auto-scroll focused inputs above the virtual keyboard. Dismiss keyboard on scroll.

### 3. Zero-Friction Flow & Form UX
- **Auto-Submit on Complete**: When a fixed-length input reaches complete length (4/6-digit OTP, PIN, 2FA code), automatically trigger verification without requiring manual button clicks.
- **Auto-Advance & Auto-Focus**: Focus first input on mount, advance focus to next slots seamlessly, and support backspace navigation.
- **Native Autofill**: Provide platform autofill (`autocomplete="one-time-code"`, SMS autofill, credential managers).
- **Smart Inline Validation Timing**: Never shame prematurely on first keystroke. Validate on `blur` after user interaction; switch to live re-validation as the user corrects errors.
- **Non-Blocking Error Recovery**: Prefer non-blocking undo toasts over interruptive confirmation modals for non-destructive actions.

### 4. Layout Stability & State UX
- **Zero Layout Shift (CLS = 0)**: Geometric skeleton loaders strictly mirroring final content height and layout. Never use generic centered spinners that collapse or push page content on load.
- **Optimistic UI Updates**: Immediately reflect user actions in the UI, sync asynchronously in background, and roll back gracefully with an undo toast if the network fails.
- **Progressive Disclosure**: Keep primary interfaces focused; disclose secondary controls via collapsible sheets, drawers, or tabs. Meaningful zero-states with clear primary CTAs.

### 5. Tactile Physics, Haptics & Delight
- **Tangible Controls**: Replace flat linear sliders with draggable dial wheels, rotary tickers, or physical pickers with magnetic tick snapping.
- **Critically-Damped Springs**: Ban cartoonish/bouncy rubber-band springs. Mandate snappy, critically damped motion (damping: 30, stiffness: 300, mass: 0.8, zero wobble).
- **The 300ms Rule (Emil Kowalski / animations.dev)**: All UI micro-interactions, dropdowns, sheet reveals, and state changes must stay strictly under 300ms (sweet spot: 150ms to 250ms).
- **Ease-Out Deceleration**: Entering UI elements must use `ease-out` or custom cubic-bezier curves (e.g. `cubic-bezier(0.16, 1, 0.3, 1)`).
- **Tactile Button Press**: Every button must compress on active state: `active:scale-[0.97]` with fast transition (`duration-75` to `100ms`).
- **Contextual Tooltip Delays**: Initial hover requires a 300ms - 500ms delay to avoid accidental flicker; subsequent adjacent tooltips hovered in sequence open instantly with 0ms delay.
- **Native Haptics**: Selection tick on dials, light impact on primary CTA press, error buzz on validation failure. Respect `prefers-reduced-motion`.

### 6. Art Direction & Closed-Loop Visual Verification
- **Taste Skill Art Direction**: Commit to one of four profiles: Technical Data Ledger (monospace, dense data, slate borders), Editorial Monospace (serif headings, monospace metadata, negative space), Crisp Modernist (geometric grotesque, authentic photography), or Brutalist Minimalist (black/white, structural gridlines).
- **Production Motion Stack & Curated Styles**: For marketing/editorial web builds, pair `gsap` (timeline/scroll choreography), `lenis` (smooth damped scroll inertia, guarded by `prefers-reduced-motion`), and `react-bits` / `21st.dev` bespoke component patterns instead of generic SaaS templates. Commit to an intentional visual/motion style (Kinetic Type, Split-Flap, Blueprint, Bauhaus, Art Deco, Neo-Brutalism, Isometric, ASCII, Paper Cutout, Physics, Clay 3D, Pixel Art, Hand-Drawn, Particles, Liquid Morph) while honoring all Anti-AI bans.
- **Closed-Loop Visual Verification**: Token alignment, multi-viewport headless render (Mobile 390px, Tablet 768px, Desktop 1440px via Figma MCP / Browser DevTools / Playwright), automated inspection of touch targets (48x48dp+) and WCAG AA contrast (4.5:1), and iterative polish before handoff.
- **4-State Feedback**: Every interactive element explicitly handles Idle, Interacting, Evaluating, and Resolution (success/error).

### 7. Mandatory Craft Audit Checklist
Before declaring any design or frontend task done, verify:
1. *Zero Emojis*: All Unicode emojis purged across UI and copy.
2. *Anti-AI Aesthetic*: Purple gradients, text clips, floating pills, and copy fluff purged.
3. *Anti-Category Averages*: 3-line eyebrow headers, 3 equal feature cards, and beige clichés eliminated.
4. *Anti-Nesting (Whitespace Breathes)*: "Cards on cards on cards" replaced by surface elevation and negative space.
5. *Typographic Intent*: Font pairing deliberate without mindless Inter font monotony.
6. *Single Accent*: Color discipline maintained with neutral grayscale surfaces.
7. *Thumb Zone & Touch*: Touch targets 48x48dp+ and actions placed in the thumb zone.
8. *Zero-Friction*: Auto-advance/auto-submit/autofill wired up.
9. *Form Timing*: Inline validation deferred to blur without premature shaming.
10. *Layout Stability*: Skeleton loaders matching final content geometry without CLS.
11. *Motion Physics (300ms & Scale)*: Micro-interactions under 300ms, entering from scale(0.95), GPU transforms only.
12. *Visual Verification*: Layout verified via rendered screenshot inspection across viewports.

---

## Vibe-Coded App Pre-Launch Security Standard

Mandatory for all Web, Mobile, Backend, and Cloud apps. Functional code is never secure by default.

### 1. The Critical 7 Non-Negotiables (Zero Tolerance)
1. **Zero Client Secrets**: API keys, webhooks, and private tokens must live in server-side env vars only. Never bundle into client JS or mobile binaries.
2. **Public Database Key Only**: Client apps must only use public/anon DB keys. Service role/admin keys that bypass RLS are strictly forbidden on clients.
3. **Universal Row-Level Security (RLS)**: Every DB table must have RLS explicitly enabled with strict user-ownership policies. Never use allow-all or `USING (true)`.
4. **BOLA / IDOR Verification**: Verify user ownership on every record ID lookup, edit, or delete. Never assume authentication implies authorization.
5. **Server-Enforced Rate Limiting**: Protect auth endpoints (login/signup/reset) and billable routes (AI inference/SMS/email) with strict server-side rate limits.
6. **Hard Billing Caps & Alerts**: Enforce hard spending caps and proactive billing alerts on all pay-per-request third-party APIs.
7. **Parameterized Queries**: Never concatenate user input into queries. Enforce parameterized queries or safe ORM methods to eliminate SQL/NoSQL injection.

### 2. Fullstack & Mobile Security Guardrails
- **Mass Assignment Defense**: Endpoints strictly validate incoming payloads against allowlists (Zod schemas). Strip administrative fields (`role`, `is_admin`, `status`, `balance`).
- **No Client-Side Paywall Illusions**: Never rely on frontend conditionals (`isPro ? <Feature /> : <PaywallModal />`). Every protected API endpoint independently verifies user tier, session, and subscription on the server.
- **Serverless Database Connection Pooling**: In serverless runtimes (Vercel, Lambda, Cloudflare), never open raw unbounded connections per request. Enforce an external transaction-mode connection pooler (PgBouncer, Supabase Pooler, Prisma Accelerate) and cache database client singletons across warm invocations.
- **Session & Token Storage**: Store auth tokens exclusively in `httpOnly`, `secure`, `sameSite` cookies on Web, and hardware-backed secure storage (`Keychain` on iOS, `Keystore` / `EncryptedSharedPreferences` on Android) on Mobile. Never use `localStorage` or unencrypted `AsyncStorage`.
- **Payment & Financial Integrity**: Verify cryptographic webhook signatures on every payment event before fulfilling orders. Enforce database-backed idempotency keys on payment webhooks to prevent duplicate fulfillment upon network retries. Compute all prices, tiers, and checkout amounts server-side.
- **Input Validation & Output Hygiene**: Validate type, length, and format on every input. Automatically escape user-generated content; ban raw HTML injections (`dangerouslySetInnerHTML`). Restrict file uploads by validating MIME type and size on the server; store in non-executable private cloud storage with short-lived signed URLs for downloads. Never return sensitive fields (password hashes, internal flags, PII) in API payloads.
- **AI Safety & OWASP Top 10 for LLMs**: Separate system prompts from user input to defend against prompt injection. Treat data retrieved from scraping, emails, and documents as untrusted user input. Enforce document-level RBAC on vector database RAG. Sanitize model outputs before display; never execute model output as code. Enforce per-user token quotas. Require human confirmation before executing high-stakes destructive agent tools.
- **Production Infrastructure & Mobile Integrity**: Force HTTPS with HSTS and security headers (`CSP`, `X-Frame-Options`, `X-Content-Type-Options`). Disable debug mode, strip source maps, and block public access to `.git` folders. Validate deep links server-side. Audit dependencies against package hallucination (slopsquatting) with `npm audit`. Isolate non-deterministic SSR values (`new Date()`, `Math.random()`, `localStorage`) in client effects to eliminate hydration mismatches.

### 3. Pre-Launch Security Audit Gate
Before declaring any application, feature, or backend task complete, run this 6-point audit:
1. *Secrets*: Are all keys server-side and excluded from git?
2. *Database*: Is RLS active on every table without `USING (true)`?
3. *Access*: Is record-level ownership checked on every ID lookup?
4. *Limits*: Are auth and expensive routes rate-limited with billing alerts?
5. *Hygiene*: Are inputs validated, outputs trimmed, and queries parameterized?
6. *Tokens*: Are cookies `httpOnly` or tokens in native Keychain/Keystore?

---

## Vibe-Coded App Production Launch, Conversion & Compliance Standard

Mandatory for all production apps and websites. Functional code is incomplete without conversion mechanics, resilience, and compliance.

### 1. Conversion Architecture & Friction Removal
- **Sticky Mobile CTAs**: Anchor primary progression actions ("Add to Cart", "Book Service", "Get Started") to the bottom of mobile viewports within thumb reach.
- **Guest Checkout / Deferred Auth**: Never force mandatory registration before a user can transact or test value. Support guest checkout and deferred account creation.
- **Upfront Pricing Transparency**: Show total costs, shipping, and fees early in the flow. Banish surprise fees at final checkout.
- **Idempotency & Duplicate Prevention**: Disable action buttons immediately upon click, generate unique idempotency keys on payment submissions, and prevent duplicate database mutations.
- **Humanized Error Messages**: Replace raw technical errors (`ERR_PAYMENT_402`, database constraint strings) with plain, actionable guidance.
- **Minimal Form Fields**: Ruthlessly remove non-essential fields; single-column layouts only.

### 2. High-Value UX Utilities & Editorial De-Slop
- **Draft Autosave**: Automatically persist draft form input in local storage to prevent data loss on accidental navigation or refresh.
- **Credential Controls**: Provide accessible show/hide password visibility toggles on all password fields.
- **Copy Utilities**: Add one-click copy buttons with immediate visual feedback ("Copied") for codes, links, and snippets.
- **Search & Navigation Shortcuts**: Implement global command palettes (`Cmd+K` / `Ctrl+K`), sticky breadcrumbs, and floating tables of contents with scroll-spy on long-form content.
- **Grounded Value Propositions**: Replace vague AI slogans ("Revolutionizing the future of...") with blunt, specific value propositions that state what the business actually does and where (e.g. "Same-day plumbing in Sydney's Inner West").
- **Zero Fabricated Social Proof**: Strictly ban made-up statistics ("10,000+ happy clients", "99.9% satisfaction") and fake user testimonials. Use verified customer reviews or omit until earned.
- **Concrete Services & Visible Contact**: Detail exact service offerings, pricing tiers, response time commitments ("Replies within 2 hours"), and visible contact details (phone, email, operating hours, address). Maximum 3 brand colors, left-aligned readable paragraphs, real photography, zero decorative stock illustrations or random floating blobs.

### 3. Technical SEO, Resilience & Legal Compliance
- **Recovery Architecture**: Provide custom 404 pages with clear links back to primary functional areas, plus post-submission thank-you confirmation pages with explicit next steps.
- **Semantic Metadata**: Enforce unique `<title>`, `<meta name="description">`, Open Graph social cards (`og:image`), semantic breadcrumbs, image `alt` attributes, and JSON-LD structured data (`schema.org`).
- **Operational Hygiene**: Ensure valid `robots.txt`, dynamic `sitemap.xml`, database query indexing, large query pagination, response caching, and uptime monitoring.
- **Essential Policy Pages**: Every production app must provide accessible Privacy Policy, Terms and Conditions, and Refund Policy pages.
- **Consent & Minimization**: Implement compliant cookie consent banners where tracking is used, provide explicit opt-in checkboxes on lead forms, and strictly practice data minimization (collect only data necessary for service fulfillment).
- **Accessibility Invariants**: Guarantee WCAG AA color contrast, full keyboard navigation, screen reader accessibility, and verified copyright licensing on all media assets.

---

## Universal Engineering Reliability & Bug Defense Standard

Mandatory across frontend, mobile, desktop, backend, and database domains to eliminate runtime failures, platform quirks, and concurrency traps.

### 1. Frontend & React Runtime Invariants
- **Async Race Condition Defense**: Every search-as-you-type, tab-switch, or paginated fetch MUST use `AbortController` to cancel out-of-order network responses. Never allow stale responses to overwrite newer state.
- **Stale Closure Prevention**: Always use functional updates (`setCount(prev => prev + 1)`) in callbacks, timers, and listeners. Accurately declare all dependencies or use `useRef` for mutable values.
- **Layout Thrashing Prevention**: Never interleave DOM geometry reads (`offsetHeight`, `getBoundingClientRect`) with writes (`style.width`) in loops. Batch all reads first, then writes.
- **Form State Integrity**: Never mix controlled and uncontrolled inputs. Always initialize inputs with defined default values (`""`, never `undefined` or `null`).

### 2. Mobile & Cross-Browser Quirks (iOS Safari & Android)
- **Dynamic Viewport Height**: Never use `height: 100vh` on mobile full-screen views (which causes vertical clipping when browser address bars toggle). Always use modern `height: 100dvh` (with `100vh` fallback) or `100svh`.
- **iOS Auto-Zoom Prevention**: Every form input (`input`, `select`, `textarea`) on mobile MUST have `font-size: 16px` or larger. Never use sub-16px input fonts on mobile viewports to prevent iOS Safari from automatically zooming into the page on focus.
- **Strict Date Parsing**: Never pass non-standard date strings with slashes (`MM/DD/YYYY`) or space-separated timestamps (`YYYY-MM-DD HH:mm:ss`) to `new Date()`. Always use strict ISO 8601 (`YYYY-MM-DDTHH:mm:ss.sssZ`) to prevent `Invalid Date` crashes on iOS Safari.
- **Overscroll Containment**: Apply `overscroll-behavior: contain` on modals, bottom sheets, and drawers to prevent background page rubber-banding on iOS touch devices.

### 3. Windows & Cross-Platform Filesystem Invariants
- **Path Normalization**: Never hardcode slashes. Use `path.resolve()` / `path.join()` for disk operations, and normalize backslashes (`replace(/\\/g, '/')`) for URLs and manifests.
- **Windows File Locking (EBUSY / EPERM)**: When deleting or overwriting files, implement exponential backoff retries to handle transient Windows locks (`EBUSY` / `EPERM`) held by indexers or antivirus.
- **Reserved Device Filenames**: Sanitize uploads and archive paths against legacy Windows reserved names (`CON`, `PRN`, `AUX`, `NUL`, `COM1-9`, `LPT1-9`) to prevent filesystem crashes and traversal exploits.

### 4. Backend, Concurrency & Financial Math Invariants
- **N+1 Query Elimination**: Never query related records in loops (N+1). Always use eager loading (Prisma `include`, Drizzle joins, or `where: { id: { in: ids } }`) in a single query.
- **Transaction Deadlock Defense**: Keep DB transaction blocks minimal; perform network calls and calculations beforehand. Catch transaction deadlock errors (Prisma `P2034`) and retry with backoff.
- **Financial Precision (Cents Integer Rule)**: Never perform currency math with JS floating-point numbers (`0.1 + 0.2 !== 0.3`). Store and calculate all money as integers in cents or use `Decimal.js`.
- **Global Error Containment**: Catch `unhandledRejection` and `uncaughtException` at the process level to log diagnostics and gracefully drain connections, preventing silent hanging processes.
- **Real-User Edge Cases**: Normalize emails (`trim().toLowerCase()`) before save/lookup; store timestamps in UTC and render in user's local timezone; convert iPhone HEIC/HEIF uploads to JPEG/WebP preserving EXIF orientation; fulfill Stripe orders inside verified webhooks (never on client `/success` pages); configure SPF/DKIM/DMARC and Apple Private Relay for transactional email; include in-app account deletion (App Store 5.1.1(v) / GDPR).
