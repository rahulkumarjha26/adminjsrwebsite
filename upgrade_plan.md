This document serves as a master prompt to guide a coding agent in implementing next-level visual, interactive, and structural improvements to the single-file website (
index.html
).

Copy the block below and feed it to your development agent to initiate the upgrade.

markdown

# Role & Objective
You are a Senior Frontend Architect and Creative Developer. Your task is to execute a comprehensive, high-fidelity design and UX upgrade on the single-file website `index.html`. You must polish its visual layout, resolve critical performance/interaction bugs, ensure strict accessibility, and implement premium micro-animations that wow the user—all while preserving existing content, SEO structures, and standard forms.
---
## Part 1: Critical Architectural & UX Bug Fixes
Before adding visual polish, resolve these design-breaking logic issues in `index.html`:
### 1. The Touch-Laptop Cursor Invisibility Bug
*   **Issue:** The current JS disables the custom cursor on devices returning `navigator.maxTouchPoints > 0` (e.g. touch-screen Windows/Chrome OS laptops), but the CSS style `a, button, .magnetic-el, .pod-icon, .pod-primary { cursor: none !important; }` is applied globally. On these hybrid laptops, when users hover a mouse/trackpad over interactive elements, the cursor disappears completely, rendering the site unusable.
*   **Fix:**
    1.  Remove global `cursor: none !important` declarations from elements.
    2.  Add a `.has-custom-cursor` class to the `<body>` element *only* inside the JS conditional checking if it's a mouse-only device.
    3.  Scope the `cursor: none` styles under this class:
        ```css
        body.has-custom-cursor, 
        body.has-custom-cursor a, 
        body.has-custom-cursor button, 
        body.has-custom-cursor .magnetic-el, 
        body.has-custom-cursor .pod-icon, 
        body.has-custom-cursor .pod-primary {
            cursor: none !important;
        }
        ```
    4.  Refine the check to dynamically detect mouse movement before enabling the custom cursor class (e.g., using a one-time `mousemove` listener).
### 2. Custom Cursor Layout Thrashing
*   **Issue:** The custom cursor currently updates `.style.left` and `.style.top` on `mousemove` and `requestAnimationFrame`. This forces a layout reflow (layout -> paint -> composite) on every frame, causing performance degradation and scroll jank.
*   **Fix:** Replace top/left manipulation with hardware-accelerated CSS 3D transforms:
    ```javascript
    // Use transform translate3d for GPU acceleration
    cDot.style.transform = `translate3d(${mouseX - dotRadius}px, ${mouseY - dotRadius}px, 0)`;
    cRing.style.transform = `translate3d(${ringX - ringRadius}px, ${ringY - ringRadius}px, 0)`;
    ```
### 3. Anatomy of Accuracy Connection Line Alignment
*   **Issue:** The SVG connector line calculations in `#anatomy-section` get misaligned if the user resizes the window or if flex layout wraps.
*   **Fix:**
    1.  Wrap the resize listener inside a throttled or debounced function.
    2.  If the SVG connector path is active, trigger `getCenter` recalculation on window `resize` and redraw the connector path dynamically.
### 4. Google Apps Script Form CORS & Payload Safety
*   **Issue:** Submitting files and fields via a standard JSON POST fetch to Google Apps Script often fails due to CORS preflight checks (preflight OPTIONS request rejected by GAS).
*   **Fix:**
    1.  Limit local file uploads to `10MB` and trigger a clear UI validation warning if exceeded.
    2.  Send form data using URL-encoded format (`application/x-www-form-urlencoded`) or a fallback to multi-part if needed, or adjust the script endpoint structure to support JSONP/simple redirects where applicable. Let the status container reflect distinct error states clearly.
---
## Part 2: Premium Design System & Token Modernization
Upgrade the CSS Custom Properties at the top of the file to use structured HSL palettes. This ensures seamless transparency scaling, harmonious dark/light mode shifts, and premium color matching.
```css
:root {
    /* Fonts */
    --font-display: 'Bricolage Grotesque', sans-serif;
    --font-body: 'Manrope', sans-serif;
    --font-mono: 'Courier New', monospace;
    /* Light Theme (Sophisticated Warm Cream & Indigo) */
    --hue-primary: 243; /* Indigo */
    --hue-warm: 38;    /* Amber */
    --bg-primary-hsl: 40, 12%, 96%;        /* #F8F7F4 */
    --bg-surface-hsl: 0, 0%, 100%;          /* #FFFFFF */
    --bg-elevated-hsl: 40, 10%, 93%;        /* #F1F0EC */
    --text-primary-hsl: 222, 47%, 11%;      /* #0F172A */
    
    --bg-primary: hsl(var(--bg-primary-hsl));
    --bg-surface: hsl(var(--bg-surface-hsl));
    --bg-elevated: hsl(var(--bg-elevated-hsl));
    --text-primary: hsl(var(--text-primary-hsl));
    --text-secondary: hsla(var(--text-primary-hsl), 0.78);
    --text-muted: hsla(var(--text-primary-hsl), 0.55);
    --accent: hsl(var(--hue-primary), 75%, 66%); /* #6366F1 */
    --accent-hover: hsl(var(--hue-primary), 75%, 58%);
    --accent-warm: hsl(var(--hue-warm), 92%, 50%); /* #F59E0B */
    --accent-warm-hover: hsl(var(--hue-warm), 92%, 42%);
    --accent-glow: hsla(var(--hue-primary), 75%, 66%, 0.14);
    --accent-warm-glow: hsla(var(--hue-warm), 92%, 50%, 0.12);
    /* Glassmorphism Defaults */
    --glass-bg: hsla(var(--bg-surface-hsl), 0.7);
    --glass-border: hsla(var(--hue-primary), 75%, 66%, 0.08);
    --glass-blur: 20px;
    
    --border: hsla(var(--hue-primary), 75%, 66%, 0.08);
    --border-strong: hsla(var(--hue-primary), 75%, 66%, 0.18);
    
    --shadow-card: 0 4px 6px -1px hsla(var(--hue-primary), 75%, 66%, 0.04), 
                   0 20px 40px -5px hsla(var(--hue-primary), 75%, 66%, 0.08);
}
[data-theme="dark"] {
    --bg-primary-hsl: 240, 50%, 6%;        /* #08081A */
    --bg-surface-hsl: 240, 36%, 14%;       /* #16162F */
    --bg-elevated-hsl: 240, 31%, 19%;      /* #212140 */
    --text-primary-hsl: 40, 10%, 93%;      /* #F1F0EB */
    
    --bg-primary: hsl(var(--bg-primary-hsl));
    --bg-surface: hsl(var(--bg-surface-hsl));
    --bg-elevated: hsl(var(--bg-elevated-hsl));
    --text-primary: hsl(var(--text-primary-hsl));
    --text-secondary: hsla(var(--text-primary-hsl), 0.85);
    --text-muted: hsla(var(--text-primary-hsl), 0.60);
    --accent: hsl(var(--hue-primary), 89%, 74%); /* #818CF8 */
    --accent-hover: hsl(var(--hue-primary), 89%, 82%);
    --accent-warm: hsl(var(--hue-warm), 96%, 60%); /* #FBBF24 */
    --accent-warm-hover: hsl(var(--hue-warm), 96%, 70%);
    --accent-glow: hsla(var(--hue-primary), 89%, 74%, 0.22);
    --accent-warm-glow: hsla(var(--hue-warm), 96%, 60%, 0.15);
    --glass-bg: hsla(var(--bg-surface-hsl), 0.7);
    --glass-border: hsla(var(--hue-primary), 89%, 74%, 0.12);
}
Rule: Standardize focus rings (outline: 2px solid var(--accent); outline-offset: 4px;) for all buttons, links, and form fields to achieve AAA accessibility.
Part 3: Component-by-Component Visual Polishing
Execute detailed styling refactors on these sections to ensure high visual excellence:

1. Navigation & Header
Visual Polish: Use CSS Glassmorphism variables for .main-nav.scrolled. Add a very subtle bottom border (border-bottom: 1px solid var(--glass-border)).
Interaction: Smooth out the mobile nav slide-in; ensure container transitions use cubic-bezier(0.16, 1, 0.3, 1) for dynamic elastic expansion.
2. Main Hero Section
Visual Polish: Enhance the text contrast on decorative blobs. Let the background blobs drift at slightly differing rates to create depth.
Typography: The main title (.main-hero-title) spans must feel fluid. Ensure text-shadow overlays are kept to a minimum to keep text crisp.
3. Manifesto (Living Dictionary Card)
Visual Polish:
Ensure the page flip animation feels physical: add subtle back-lighting to the card during the flip, and a faint gradient shadow behind it.
For mobile sizes, ensure the card maintains aspect ratios correctly and does not overflow. Prevent text sizing clipping.
Interaction: Enable click fallback for desktop so users who click the dictionary word also toggle the translation container.
4. Trust Section (Modals/Drawers Expansion)
UX Enhancement: Currently, the detailed information in write_ups.md (for NDA Safe, Data Secure, ISO Standards, and Quality Checked) is condensed. Build interactive slide-out side-drawers or modal dialogs for these trust elements.
When a user clicks a Trust Card, open a modern overlay showing the comprehensive breakdown (e.g. details on GDPR compliance, access controls, multi-layer reviews).
Animate the entrance of these drawers with a smooth translate transition (transform: translateX(100% -> 0)). Maintain proper focus-trapping inside the opened modal.
5. Anatomy of Accuracy Interactive Section
Visual Polish: Enhance the highlight states on both the source English text and target Japanese text.
Math Check: Ensure the SVG bounding client calculations (rect.left - svgRect.left) handle relative wrapping correctly on tablets and vertical screens.
6. Vault (Services Section)
Visual Polish: Integrate subtle grid lines in the background. The cards (.exp-card) must transition smoothly. Enhance card elements with glassmorphism backgrounds.
Aesthetics: Ensure Font Awesome icons use smooth hover transformations (e.g., rotating or lifting 2-3 pixels).
7. BPO Services Section
Visual Polish: Align the layout elements into an asymmetrical grid that dynamically flips columns on alternate blocks.
Hover State: Give the image wrapper containers a subtle scale transition (transform: scale(1.03) on hover) and a rich color saturation adjustment.
8. Team Section (Network Visualization)
Visual Polish: Currently, the team section is just a simple text block with badges. Refactor this into a visually striking interactive network/world map:
Introduce an SVG or styled CSS coordinate map showcasing locations (e.g., India, UK, USA, Singapore, Japan).
Use pulse animations (@keyframes pulse) on key node centers representing native linguist hubs.
When hovering over a node, show a clean, floating tooltip detailing the language coverage and domain focus for that location.
9. Resonance (Testimonials & SVG Signatures)
Micro-Interaction: The testimonial cards feature inline SVG paths representing signatures.
Animate these signatures so they draw themselves in real-time when the card enters the viewport.
Use the stroke-dashoffset technique triggered by a viewport-based IntersectionObserver. Set:
css

.sig-path {
    stroke-dasharray: 300;
    stroke-dashoffset: 300;
    transition: stroke-dashoffset 2s cubic-bezier(0.4, 0, 0.2, 1);
}
.active-card .sig-path {
    stroke-dashoffset: 0;
}
10. Command Footer & Request Form
Visual Polish: Clean up input styles. Apply:
css

input, select, textarea {
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid var(--border);
    border-radius: 8px;
    color: var(--text-primary);
    transition: all 0.3s ease;
}
input:focus, select:focus, textarea:focus {
    border-color: var(--accent);
    background: rgba(255, 255, 255, 0.04);
    box-shadow: 0 0 0 4px var(--accent-glow);
}
Feedback: Give the status box (#form-status) entry animations (opacity and height expand) to avoid jarring layout jumps.
Part 4: CSS Scroll-Driven Animations & Performance Tweaks
Use modern CSS standards to remove performance-heavy JS scroll listeners.

1. Native Scroll-Driven Animations
Where supported (check with @supports (animation-timeline: scroll())), implement the scroll progress bar, the sticky nav transition, and background parallax using pure CSS:

css

@supports (animation-timeline: scroll()) {
    /* CSS Scroll Progress Bar */
    .scroll-progress {
        width: 100%;
        scale: 0 1;
        transform-origin: left;
        animation: grow-progress linear;
        animation-timeline: scroll(root);
    }
    @keyframes grow-progress {
        from { transform: scaleX(0); }
        to { transform: scaleX(1); }
    }
}
If supported, bypass the JS listeners for those elements to save CPU cycles.

Part 5: Quality & Verification Requirements
Ensure the output is clean, bulletproof, and fully compliant:

Zero External Dependencies: Do not import any new Javascript frameworks. Everything must remain in Vanilla CSS/JS inside the single index.html file.
No Placeholders: All assets must be real, functional, and visually flawless. Use high-quality imagery or direct SVG generation.
Preserve Comments: Keep existing organizational comments and structural layout identifiers intact.
Responsive Validation: Test layouts down to 320px width (iPhone SE) and up to 2560px (Ultra-wide monitors).

***
Now, proceed to execute these changes step-by-step to achieve design perfection!