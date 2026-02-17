# 🏄 Tefila App - Windsurf Guide
## Complete Setup + All Unresolved Issues

---

# PART 1: HOW TO USE WINDSURF

## Getting Started
1. Download Windsurf from https://codeium.com/windsurf
2. Install it (it looks similar to VS Code but is simpler)
3. **File → Open Folder** → select the `TefilaSite` folder
4. You'll see all files in the left sidebar

## Previewing Your Site
- Install **"Live Server"** extension:
  1. Click Extensions icon (left sidebar, 4 squares)
  2. Search **"Live Server"** by Ritwick Dey → Install
  3. Right-click `index.html` → **"Open with Live Server"**
  4. Browser opens with your site — auto-refreshes on every save!

## Windsurf's AI: Cascade
Windsurf uses one unified AI called **Cascade**. Open it with **Ctrl+L** (or click the chat icon).

### Two Modes:

**1. Chat Mode** — Ask questions, get explanations
- "Why is there a gap between the header and content?"
- "What CSS files control the header?"
- Does NOT edit files

**2. Write Mode (default)** — Tell it what to fix and it does it
- It reads your files, plans changes, edits code, runs terminal commands
- This is the one you'll use most
- It shows you diffs before applying — click **Accept** or **Reject**

### Key Differences from Cursor:
- Windsurf has **one unified chat** instead of separate Ask/Edit/Agent modes
- It automatically decides whether to read, edit, or run commands
- It remembers your full conversation context better
- It can run terminal commands (like starting a server) directly

### Pro Tips:
- **Be specific**: "Fix the 50px gap between header and content" beats "fix the gap"
- **One issue at a time**: Fix → Preview → Confirm → Next
- **Tag files**: Type `@index.html` to point Windsurf at a specific file
- **Use @codebase**: Type `@codebase` to let it search your whole project

---

# PART 2: ALL UNRESOLVED ISSUES

Copy each prompt below into Windsurf's Cascade chat (Write mode), one at a time. Preview after each fix.

---

## 🔴 FIX 1: GAP BETWEEN HEADER AND CONTENT
**The #1 issue — asked 10+ times, never fully fixed**

### Prompt for Windsurf:
> There is a large visible gap between the fixed header (90px tall) and the page content below it. This happens on the home page and all other views. The content should start IMMEDIATELY under the header with minimal clearance.
>
> Search ALL CSS files (@styles.css @styles_v2.css @overhaul.css @recovery.css @restoration.css) AND the inline `<style>` blocks in @index.html for any margin-top, padding-top, or gap on: `#main-container`, `.content-area`, `.view`, `.home-container`, `.welcome-banner`, `section.view`, `#home-view`, `#prayer-view`.
>
> The fix: `#main-container` and `.content-area` should have `margin-top: 95px; padding-top: 0`. Every `.view` div should have `padding-top: 0; margin-top: 0`. Remove ALL conflicting rules across ALL files. The nuclear CSS block at the end of index.html should be the single source of truth.

---

## 🔴 FIX 2: CLOCK WIDGET OVERFLOWING HEADER

### Prompt for Windsurf:
> The clock widget (`.clock-wrapper`, pill/oval shape) is too big and overflows out of the 90px header. It should fit ENTIRELY inside.
>
> Fix: Set `.clock-wrapper { transform: scale(0.6); transform-origin: center; overflow: hidden; margin: 0; padding: 5px 10px; }`. Also add `overflow: hidden` to `header.navbar`. Search all CSS files for conflicting `.clock-wrapper` rules and consolidate them.

---

## 🔴 FIX 3: JUMP TO SECTION NOT WORKING

### Prompt for Windsurf:
> The "Jump to Section" feature is completely broken:
> 1. Clicking the ☰ button on prayer cards on the home page does nothing
> 2. The floating ≡ button doesn't appear when inside a prayer view
> 3. Even if it appears, clicking it doesn't open a table of contents
>
> Check in @app.js:
> - `window.openSectionModal(prayerType, event)` must work and show a modal with sections
> - `window.openCurrentPrayerSections()` must work when the floating FAB is clicked
> - The `#section-nav-fab` must NOT have `display: none !important` in CSS (this blocks JS from showing it — only use `display: none` without !important)
> - `window.PRAYER_TEXTS` in @data.js must have section data with IDs
> - The setupView override at the bottom of app.js should set `fab.style.display = 'flex'` when entering a prayer view
>
> In @index.html, verify the onclick handlers on prayer card ☰ buttons actually call `window.openSectionModal('shacharit', event)` etc.

---

## 🔴 FIX 4: FONT SIZE A+/A- CHANGES EVERYTHING

### Prompt for Windsurf:
> When pressing A+ or A- to adjust font size, it changes the size of EVERYTHING — titles, buttons, cards, navigation — not just the prayer text.
>
> Root cause in @app.js:
> 1. There may be DUPLICATE `adjustPrayerFont` functions. Keep only the FIRST one (around line 41) that targets specific prayer containers
> 2. There may be an `adjustFont` function that sets `document.body.style.fontSize` — this is WRONG. It should only call `adjustPrayerFont`
> 3. The second adjustPrayerFont (if it exists around line 270) uses `.hebrew-text` selector which is too broad (matches home page card text too) — REMOVE it
>
> The correct behavior: Only elements with class `.prayer-paragraph` inside `#prayer-view`, `#birkat-view`, `#meein-view`, `#haderech-view` should change size. Also `.optional-prayer` and `.prayer-instruction` should scale. Titles (h2, h3, h4) should NEVER scale. Use only the CSS variable `--prayer-font-size`.

---

## 🟠 FIX 5: PARCHMENT MODE BROKEN

### Prompt for Windsurf:
> Parchment mode (toggled by the 📜 button) either doesn't work or affects the entire page including the header.
>
> `window.togglePrayerBg()` in @app.js toggles class `parchment-active` on body. The CSS rules should:
> - ONLY change `.prayer-content-wrapper` and prayer view `.glass-card` backgrounds to parchment gradient
> - ONLY change prayer text color to dark brown #3d2c1e
> - NOT change header.navbar background (remove any rule that sets `background: inherit` on the header in parchment mode)
> - NOT change home page cards or sidebar
>
> Check all CSS files for `body.parchment-active` rules and make sure header keeps its original color.

---

## 🟠 FIX 6: SUBSCRIBE SCROLL BUG

### Prompt for Windsurf:
> Clicking Subscribe in the footer newsletter causes the page to scroll weirdly — header jumps, user ends up at wrong position.
>
> In @app.js, `window.handleSubscribe(event)` should: call `e.preventDefault()` first, then `window.setupView('thank-you')`, then `window.scrollTo(0, 0)`. Make sure the form has `onsubmit="return window.handleSubscribe(event)"` and type="button" or proper prevention so the form never actually submits.

---

## 🟠 FIX 7: TOOLBAR BUTTONS TOO BIG

### Prompt for Windsurf:
> The Easy Access Toolbar buttons (A+, A-, Align, Parchment, Width) are too large. Make them compact:
> ```css
> #easy-access-toolbar { position: sticky; top: 90px; padding: 8px; gap: 5px; }
> #easy-access-toolbar button { padding: 6px 10px; font-size: 0.85rem; min-width: auto; }
> ```

---

## 🟡 FIX 8: LIGHT MODE HEADER NOT NAVY

### Prompt for Windsurf:
> In Light Mode, the header should be navy blue (#003366) with white text. Currently it may appear white or gray. Check the theme-switching logic in @app.js and all CSS files to ensure light mode sets `header.navbar { background: #003366; color: white; }`.

---

## 🟡 FIX 9: THEME DOESN'T PERSIST

### Prompt for Windsurf:
> Theme doesn't persist after refresh. Save theme to localStorage when changed: `localStorage.setItem('theme', theme)`. On page load, read it: `const saved = localStorage.getItem('theme'); if (saved) applyTheme(saved);`. Same for parchment mode.

---

## 🟡 FIX 10: FAB ICON POSITIONS

### Prompt for Windsurf:
> The floating action buttons overlap or are positioned wrong. Set:
> - Book FAB (`#prayer-fab-container`): `bottom: 130px; left: 20px; z-index: 10000`
> - Section Nav FAB (`#section-nav-fab`): `bottom: 180px; left: 25px; z-index: 10001`
> Make sure they don't overlap each other.

---

# PART 3: FEATURES TO ADD

## 🆕 MyZmanim Widget

### Prompt for Windsurf:
> Add a Zmanim (prayer times) widget to the home page right column in `#home-zmanim-container`. Either embed MyZmanim.com in an iframe OR fetch from the Hebcal API (https://www.hebcal.com/zmanim). Show: Dawn, Sunrise, Latest Shema, Latest Shacharit, Midday, Mincha Gedola, Mincha Ketana, Sunset, Nightfall, Midnight. Make it scrollable (max-height: 500px), start from Dawn. Style it to match the app's glass card look.

## 🆕 Splash Screen

### Prompt for Windsurf:
> Add a book-opening splash screen animation when the app first loads. Show an animated book opening for 2-3 seconds with a fade transition to the main app. Use CSS animations only, no libraries.

## 🆕 Scroll Memory

### Prompt for Windsurf:
> When navigating away from home (opening a prayer), save `window.scrollY` to a variable. When `goHome()` is called, restore that scroll position with `window.scrollTo(0, savedPosition)` after a short delay.

## 🆕 Search

### Prompt for Windsurf:
> Build a search feature. The search bar on home opens a modal. User types keywords, it searches through PRAYER_TEXTS in @data.js, shows matching results. Clicking a result navigates to that prayer and scrolls to the match.

## 🆕 Tehillim Placeholder

### Prompt for Windsurf:
> Add a Tehillim (Psalms) card on the home page. When clicked, show a "Coming Soon - בקרוב" page with a nice design.

---

# PART 4: TESTING CHECKLIST

After each fix, test:

- [ ] No gap between header and content (home page)
- [ ] No gap between header and content (prayer views)
- [ ] Clock fits in header, no overflow
- [ ] Prayer card click opens prayer
- [ ] ☰ Jump button opens section modal
- [ ] Sections listed in modal with correct names
- [ ] Clicking section scrolls to it
- [ ] ≡ FAB appears when in prayer view
- [ ] ≡ FAB opens current prayer's sections
- [ ] A+ increases ONLY prayer text
- [ ] A- decreases ONLY prayer text
- [ ] Titles stay same size with A+/A-
- [ ] Home cards stay same size with A+/A-
- [ ] Parchment changes prayer area only
- [ ] Header stays normal in parchment mode
- [ ] Subscribe → Thank You (no scroll jump)
- [ ] Light mode = navy header
- [ ] Theme saved after refresh
- [ ] Book FAB at 130px, Nav FAB at 180px
- [ ] Zmanim shows prayer times
- [ ] Hebrew text correct
- [ ] Mobile responsive

---

*Open TefilaSite folder in Windsurf → Ctrl+L → paste one prompt at a time → Accept changes → Preview → Next*
