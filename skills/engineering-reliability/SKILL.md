---
name: engineering-reliability
description: "Master engineering reliability and bug defense guide. Use whenever writing, reviewing, or debugging code across frontend, mobile (iOS/Android), desktop (Windows), backend APIs, or databases. Provides production patterns for async race conditions, iOS/Safari quirks, Windows filesystem locking, database N+1 queries, transaction deadlocks, and financial precision math."
---

# Universal Engineering Reliability & Master Bug Defense Guide

This skill provides battle-tested implementation patterns, defensive code recipes, and architectural invariants designed to eliminate the most notorious runtime bugs, concurrency races, platform quirks, and failure modes across Web, iOS, Android, Windows, Node.js, and modern databases.

---

## 1. Frontend & React Runtime Defense

### A. Async Race Condition Defense (`useRaceConditionSafeSearch`)
In search-as-you-type and paginated views, out-of-order network responses overwrite current state if not cancelled.
```tsx
import { useEffect, useState } from "react";

export function useRaceConditionSafeSearch<T>(
  query: string,
  fetcher: (q: string, signal: AbortSignal) => Promise<T[]>
) {
  const [results, setResults] = useState<T[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      setLoading(false);
      return;
    }

    const controller = new AbortController();
    setLoading(true);
    setError(null);

    fetcher(query, controller.signal)
      .then((data) => {
        setResults(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          setError(err instanceof Error ? err : new Error(String(err)));
          setLoading(false);
        }
      });

    // Abort active fetch if query changes or component unmounts
    return () => controller.abort();
  }, [query]);

  return { results, loading, error };
}
```

### B. Stale Closure Prevention
Never read mutable state inside async callbacks or timers without functional updates or `useRef`:
```tsx
// Bad: Captures count = 0 forever if dependencies are omitted
const [count, setCount] = useState(0);
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1); // Stale closure bug
  }, 1000);
  return () => clearInterval(id);
}, []);

// Good: Functional state update reads latest value deterministically
useEffect(() => {
  const id = setInterval(() => {
    setCount((prev) => prev + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

### C. Layout Thrashing Prevention
Never interleave DOM geometry reads with writes:
```ts
// Bad: Forces synchronous layout recalculation on every iteration
for (const el of elements) {
  const height = el.offsetHeight; // Read (invalidates layout cache)
  el.style.width = `${height * 2}px`; // Write (forces reflow)
}

// Good: Batch reads first, then batch writes
const heights = elements.map((el) => el.offsetHeight); // Batch read
elements.forEach((el, i) => {
  el.style.width = `${heights[i] * 2}px`; // Batch write
});
```

---

## 2. Mobile & Cross-Browser Invariants (iOS Safari & Android)

### A. Viewport & Input Zoom Defense
```css
/* Prevent iOS Safari 100vh toolbar clipping */
.full-screen-layout {
  min-height: 100vh;
  min-height: 100dvh;
}

/* Prevent iOS Safari auto-zoom on input focus */
@media screen and (max-width: 768px) {
  input, select, textarea {
    font-size: 16px !important;
  }
}

/* Prevent modal overscroll rubber-banding */
.modal-container, .bottom-sheet-drawer {
  overscroll-behavior: contain;
}
```

### B. Strict ISO 8601 Date Parsing
Safari rejects dates with slashes (`MM/DD/YYYY`) or space-separated timestamps:
```ts
// Safe date parsing helper
export function parseSafeDate(dateInput: string | Date): Date {
  if (dateInput instanceof Date) return dateInput;
  
  // Normalize slashes to hyphens and ensure standard ISO format
  const normalized = dateInput.trim().replace(/\//g, "-").replace(" ", "T");
  const parsed = new Date(normalized);
  
  if (isNaN(parsed.getTime())) {
    throw new Error(`Invalid Date format: ${dateInput}. Use strict ISO 8601.`);
  }
  return parsed;
}
```

---

## 3. Windows & Cross-Platform Filesystem Defense

### A. Normalized Path Generation
```ts
import path from "node:path";

export function toNormalizedPath(...segments: string[]): string {
  // Resolves on host OS, then explicitly normalizes backslashes to forward slashes
  return path.resolve(...segments).replace(/\\/g, "/");
}
```

### B. Windows File Locking (`EBUSY` / `EPERM`) Retry Helper
```ts
import fs from "node:fs/promises";

export async function safeDeleteWithRetry(targetPath: string, retries = 5, delayMs = 150): Promise<void> {
  for (let attempt = 1; attempt <= retries; attempt++) {
    try {
      await fs.rm(targetPath, { recursive: true, force: true });
      return;
    } catch (err: any) {
      if ((err.code === "EBUSY" || err.code === "EPERM") && attempt < retries) {
        // Wait with exponential backoff for Windows file handle release
        await new Promise((resolve) => setTimeout(resolve, delayMs * Math.pow(2, attempt - 1)));
        continue;
      }
      throw err;
    }
  }
}
```

### C. Reserved Device Filename Sanitization
Windows prohibits files named `CON`, `PRN`, `AUX`, `NUL`, `COM1-9`, `LPT1-9`:
```ts
const WINDOWS_RESERVED_NAMES = new Set([
  "CON", "PRN", "AUX", "NUL",
  "COM1", "COM2", "COM3", "COM4", "COM5", "COM6", "COM7", "COM8", "COM9",
  "LPT1", "LPT2", "LPT3", "LPT4", "LPT5", "LPT6", "LPT7", "LPT8", "LPT9"
]);

export function sanitizeFilename(filename: string): string {
  const base = path.parse(filename).name.toUpperCase();
  if (WINDOWS_RESERVED_NAMES.has(base)) {
    return `safe_${filename}`;
  }
  return filename.replace(/[\<\>\:\"\/\\\|\?\*]/g, "_");
}
```

---

## 4. Database, Concurrency & Financial Math

### A. Eliminating N+1 Queries (Eager Loading Pattern)
```ts
// Bad: 1 query for users + N queries for orders inside loop
const users = await db.users.findMany();
const orders = await Promise.all(users.map(u => db.orders.findMany({ where: { userId: u.id } })));

// Good: 1 optimized single round-trip query via eager loading
const usersWithOrders = await db.users.findMany({
  include: {
    orders: true
  }
});
```

### B. Transaction Deadlock Retry Loop with Jitter
```ts
export async function executeTransactionWithRetry<T>(
  txOperation: () => Promise<T>,
  maxRetries = 3,
  baseDelayMs = 100
): Promise<T> {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await txOperation();
    } catch (error: any) {
      // P2034: Prisma write conflict or deadlock; 40P01: Postgres deadlock detected
      const isDeadlock = error.code === "P2034" || error.code === "40P01";
      if (isDeadlock && attempt < maxRetries) {
        const jitter = Math.random() * 50;
        const delay = baseDelayMs * Math.pow(2, attempt - 1) + jitter;
        await new Promise((resolve) => setTimeout(resolve, delay));
        continue;
      }
      throw error;
    }
  }
  throw new Error("Transaction failed after maximum retries");
}
```

### C. Financial Decimal Precision (Integer Cents Math)
```ts
export const MonetaryMath = {
  // Convert dollars float to exact integer cents
  toCents(dollars: number): number {
    return Math.round(dollars * 100);
  },

  // Convert cents integer to display dollars
  toDollars(cents: number): number {
    return cents / 100;
  },

  // Add cents safely without floating point drift
  add(centsA: number, centsB: number): number {
    return centsA + centsB;
  },

  // Calculate percentage discount in cents
  applyDiscount(cents: number, discountPercentage: number): number {
    return Math.round(cents * (1 - discountPercentage / 100));
  },

  // Format to currency string
  format(cents: number, currency = "USD"): string {
    return new Intl.NumberFormat("en-US", {
      style: "currency",
      currency
    }).format(cents / 100);
  }
};
```

### D. Process Crash Containment & Graceful Drain
```ts
export function registerProcessErrorGuards(onShutdown: () => Promise<void>) {
  const shutdown = async (signal: string) => {
    console.error(`Received ${signal}. Draining connections and shutting down.`);
    try {
      await onShutdown();
    } finally {
      process.exit(1);
    }
  };

  process.on("unhandledRejection", (reason) => {
    console.error("Unhandled Promise Rejection detected:", reason);
    shutdown("unhandledRejection");
  });

  process.on("uncaughtException", (error) => {
    console.error("Uncaught Exception detected:", error);
    shutdown("uncaughtException");
  });
}
```

---

## 5. Real-User Production Edge Cases

Defend against everyday user actions that break vibe-coded apps in production:

- **Email Case & Whitespace Normalization**: Always run `email.trim().toLowerCase()` before both registration/storage and login/lookup (`Ryan@gmail.com` must match `ryan@gmail.com`).
- **Real Names with Apostrophes & Unicode**: Never break on `O'Neill`, `D'Angelo`, or accented characters; enforce parameterized queries everywhere.
- **iPhone HEIC/HEIF Photo Uploads**: Desktop browsers (Chrome/Firefox) cannot render `.heic` / `.heif` images. Convert HEIC/HEIF uploads server-side (or via client WASM) to JPEG/WebP while preserving EXIF orientation.
- **UTC Storage, Local Display**: Store all dates, bookings, and timestamps in UTC (`toISOString()`), and render them in the user's local timezone via `Intl.DateTimeFormat` or explicit timezone selection.
- **Webhook-First Payment Fulfillment**: Never create orders or grant entitlements only on the client `/success` redirect (users lose signal or close tabs right after paying). Fulfill orders exclusively inside verified, idempotent server-side Stripe webhooks (`checkout.session.completed` / `payment_intent.succeeded`).
- **Transactional Email Deliverability & Apple Private Relay**: Send auth/password-reset emails from a verified custom domain with valid **SPF, DKIM, and DMARC** DNS records, and register your sending domain in **Apple Developer -> Sign in with Apple -> Email Sources** so `@privaterelay.appleid.com` addresses actually receive mail.
- **In-App Account Deletion**: Always provide an accessible `Settings -> Account -> Delete Account` flow (required by Apple App Store Guideline 5.1.1(v) and GDPR).
- **Traffic Spike Readiness**: Combine serverless DB connection pooling, response caching for public reads, and basic load testing before launch so concurrent bursts don't trigger `FATAL: sorry, too many clients already`.
- **Scrollable Drawers Under Smooth Scroll (`<dialog>` + Lenis)**: Never leave `<dialog>` drawers with implicit flex sizing or active global smooth-scroll (`Lenis`) wheel listeners while open. Set `dialog[open] { display: flex; flex-direction: column; overflow: hidden; }` with explicit bounded height (`h-dvh` / `max-h-[88dvh]`), give the scroll body `flex-1 min-h-0 overflow-y-auto overscroll-contain` + `data-lenis-prevent`, destroy/skip Lenis while paused, and verify real `mouseWheel` `scrollTop > 0` in browser automation.
