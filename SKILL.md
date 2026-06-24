---
name: premium-gym-dev
description: Mental models, anti-patterns, and one-shot directives for building high-end, dark-theme fitness websites using Next.js, Tailwind, and Framer Motion. Eliminates common SSR and mobile layout mistakes based on the BodyZone project.
---

# Premium Gym Website Development Guidelines

When building or refining a premium, dark-theme fitness website (like BodyZone), apply these mental models immediately to avoid back-and-forth iteration, SSR hydration bugs, and mobile layout clashing.

## 1. The SSR & Animation Mental Model (The Hydration Trap)
**The Problem**: Defaulting to `initial={{ opacity: 0 }}` with Framer Motion on the server causes the HTML to render invisible. If JavaScript fails or the network is slow, the user sees a blank screen.
**The Fix (One-Shot)**: Never use `initial` opacity states on critical content without a hydration gate.
*   **Implement a `useHasMounted` hook** (`const mounted = useHasMounted();`) and conditionally apply animations:
    `<motion.div animate={{ opacity: mounted ? 1 : 0 }}>`
*   **Direct DOM Mutation**: For high-frequency updates (like counters or scroll listeners), use `useRef` and directly mutate `ref.current.textContent` instead of triggering React state re-renders.

## 2. The Mobile Layout Hierarchy
**The Problem**: Absolute positioning complex hero images (like an athlete cutout) often overlays and blocks critical Call-To-Action (CTA) buttons on mobile screens.
**The Fix (One-Shot)**: 
*   Always enforce a strict mobile-first stacking order: **Brand/Text -> Athlete Image (z-index managed) -> CTA Buttons**.
*   CTA buttons must be explicitly positioned *above the fold* and in standard document flow on mobile (`relative`, not `absolute`), so they are never occluded by decorative images.

## 3. Custom Assets vs. Generic UI
**The Problem**: Using generic icons (e.g., `lucide-react` Dumbbell) for a brand's core visual identity feels cheap and templated.
**The Fix (One-Shot)**: 
*   If a visual element represents the core brand (like a barbell scroll indicator), build a custom SVG or pure CSS layout immediately. Don't default to a library icon.
*   **Scroll Indicators**: If implementing a floating scroll indicator, pull it out of the layout flow (`fixed bottom-6 left-1/2 -translate-x-1/2 z-50`) and tie it to `useScroll` so it fades out (`opacity: useTransform(scrollY, [0, 100], [1, 0])`) the moment the user interacts. Do not clutter the static hero DOM.

## 4. Typography & Accessibility Precision
**The Problem**: High-end designs break if text wraps awkwardly or if screen readers get confused by visual styling.
**The Fix (One-Shot)**:
*   Apply `text-wrap: balance` to all major headings.
*   Use `tabular-nums` on all pricing and statistics.
*   Never use block elements (`<div>`) inside inline typography elements (`<h1>`, `<p>`). Use `<span>`.
*   Hide decorative SVGs and massive background watermarks from screen readers using `aria-hidden="true"`.

## 5. Bridging the Human-AI Communication Gap
Why did we have back-and-forth iteration in the past?
*   **The Human Gap**: The user often knows *what* they want it to look like (e.g., "a cool barbell that bounces"), but doesn't always anticipate the responsive physics (how it acts on mobile vs desktop, or when scrolling).
*   **The AI Gap**: The AI often implements the easiest, literal interpretation (a generic icon in absolute position) instead of thinking 3 steps ahead to the premium, bulletproof solution (a custom fixed component with scroll-fade).
*   **The Rule**: AI must proactively design for the *edge cases* (mobile overlap, SSR failure, screen reader noise) *before* presenting the "cool" feature to the user.

---

## Author Note
This skill was extracted directly from the BodyZone website development lifecycle, designed to give future AI agents the exact mental models needed to execute premium UI/UX tasks in a single shot without causing layout regression or hydration mismatch errors.
