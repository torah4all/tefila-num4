# 📋 Tefila App - Complete Issues & Cursor Guide
## For use with Cursor AI Editor

---

# PART 1: HOW TO USE CURSOR

## Getting Started
1. Download Cursor from https://www.cursor.com/
2. Install and open it
3. Click **File → Open Folder** → select the `TefilaSite` folder
4. You'll see all your files in the sidebar (index.html, js/, css/, etc.)

## Previewing Your Site in Cursor
- Install the **"Live Server"** extension:
  1. Click the Extensions icon (4 squares) on the left sidebar
  2. Search "Live Server" by Ritwick Dey
  3. Click Install
  4. Right-click `index.html` → **"Open with Live Server"**
  5. Your site opens in the browser AND auto-refreshes when you save changes!

- Alternative: Just double-click `index.html` from Windows Explorer to open in browser

## Cursor's 3 AI Modes Explained

### 1. 💬 ASK Mode (Cmd+L or Ctrl+L)
- **What it does:** You ask questions about your code and it answers
- **When to use:** When you don't understand something or want to learn
- **Example prompts:**
  - "Why is there a gap between the header and content?"
  - "What does the adjustPrayerFont function do?"
  - "Which CSS file controls the header styling?"
- **It does NOT edit code** — it only explains

### 2. ✏️ EDIT Mode (Cmd+K or Ctrl+K)
- **What it does:** You highlight code and tell it what to change
- **When to use:** When you know WHICH code to change but need help writing it
- **How to use:**
  1. Select the lines of code you want to change
  2. Press Ctrl+K
  3. Type what you want (e.g., "make this gap smaller")
  4. It shows you a diff (green = added, red = removed)
  5. Click Accept or Reject
- **Good for:** Small, targeted fixes

### 3. 🤖 AGENT Mode (Cmd+L then switch to Agent tab)
- **What it does:** Full autopilot — it reads files, plans changes, edits multiple files, and runs commands
- **When to use:** For bigger tasks that touch multiple files
- **Example prompts:**
  - "Fix the gap between the header and content area"
  - "Make the parchment mode only affect prayer text, not the whole page"
  - "Add the MyZmanim widget to the home page"
- **This is the most powerful mode** — it's like having a developer working for you
- **TIP:** Be specific! Instead of "fix the site", say exactly what's wrong

## Pro Tips for Cursor
- **Be specific:** "Fix the 50px gap between header and content" > "fix the gap"
- **Reference files:** "In index.html, the header at line 99 is too tall"
- **One issue at a time:** Don't ask it to fix 10 things at once
- **Use Agent mode** for most of your work — it's the smartest
- **Preview after each fix** to make sure it worked before moving on

---

# PART 2: ALL UNRESOLVED ISSUES (Detailed)

Below is EVERY issue that was reported but may not be properly fixed. Use these descriptions to tell Cursor exactly what to fix.

---

## 🔴 ISSUE 1: GAP BETWEEN HEADER AND CONTENT
**Priority: CRITICAL — Asked 10+ times, never fully fixed**

**The Problem:**
There is a visible gap/space between the bottom of the fixed header and the top of the page content. On some views it's small, on others it's a huge empty space. The user should see content immediately under the header with no white/dark empty band.

**What Should Happen:**
- Header is fixed at top, height 90px
- Content area starts at exactly 95px from top (5px breathing room)
- NO padding-top on any view containers
- NO margin on .welcome-banner, .home-container, etc.
- The first piece of content (welcome text or prayer cards) should be RIGHT UNDER the header

**Technical Details:**
- `header.navbar` has `height: 90px; position: fixed; top: 0`
- `#main-container` and `.content-area` should have `margin-top: 95px; padding-top: 0`
- Every `.view` div should have `padding-top: 0; margin-top: 0`
- Check ALL CSS files — there may be conflicting rules in `styles.css`, `styles_v2.css`, `overhaul.css`, `recovery.css`, `restoration.css`, AND inline styles in `index.html`
- The nuclear CSS block at the END of index.html (before </body>) was supposed to override everything but may not cover all cases

**How to Fix in Cursor:**
> "There is a large gap between the fixed header (90px) and the page content. Search ALL CSS files and the inline styles in index.html for any margin-top, padding-top, or gap properties on #main-container, .content-area, .view, .home-container, .welcome-banner. Remove all extra spacing. Content should start at margin-top: 95px from the top of the page, with zero padding."

---

## 🔴 ISSUE 2: CLOCK WIDGET OVERFLOWING FROM HEADER
**Priority: CRITICAL**

**The Problem:**
The clock widget (pill/oval shape showing time, Hebrew date, Bdi Avad) is too large and sticks out of the header. It looks cut off or bleeds below the header bar.

**What Should Happen:**
- Clock widget fits ENTIRELY inside the 90px header
- Pill/oval shape with glass effect
- Scale it down if needed (transform: scale(0.6))
- Content inside: current time, Hebrew date, "Bdi Avad" label
- Should not push other header elements around

**Technical Details:**
- `.clock-wrapper` needs `transform: scale(0.6); transform-origin: center; overflow: hidden`
- The header should have `overflow: hidden` to clip anything that escapes
- Multiple CSS files may have conflicting clock-wrapper rules

**How to Fix in Cursor:**
> "The clock widget (.clock-wrapper) overflows out of the 90px header. Scale it down with transform: scale(0.6) and add overflow: hidden to the header. Make sure the clock fits entirely within the header bar."

---

## 🔴 ISSUE 3: JUMP TO SECTION / SECTION NAVIGATION NOT WORKING
**Priority: CRITICAL**

**The Problem:**
When clicking the "Jump" button on prayer cards (the ☰ icon on the right side of each card), nothing happens. Also, the floating hamburger button (≡) that should appear when INSIDE a prayer view either doesn't show up or doesn't open the table of contents.

**What Should Happen:**
1. On **Home Page** prayer cards: clicking the ☰ "Jump" side opens a modal listing all sections of that prayer
2. When **inside a prayer view**: a floating ≡ button appears at bottom-left (180px from bottom)
3. Clicking that ≡ button opens a table of contents modal for the current prayer
4. Clicking a section in the modal scrolls to that section in the prayer text
5. The section gets a brief highlight animation when scrolled to

**Technical Details:**
- `window.openSectionModal(prayerType, event)` — opens TOC modal for a specific prayer
- `window.openCurrentPrayerSections()` — opens TOC for whichever prayer is currently displayed
- `window.jumpTo(prayerType, sectionId)` — scrolls to a specific section
- The `#section-nav-fab` button had `display: none !important` in CSS which BLOCKED the JavaScript from showing it — this was recently changed but may still not work
- `window.PRAYER_TEXTS` in data.js must have section data for the modal to populate
- Each prayer section in the HTML needs matching IDs for scrolling to work

**How to Fix in Cursor:**
> "The Jump to Section feature is broken. When clicking the ☰ button on prayer cards, openSectionModal should fire and show a modal with prayer sections. When inside a prayer view, the #section-nav-fab button should become visible (display: flex) and clicking it should call openCurrentPrayerSections(). Check that: 1) onclick handlers are correctly attached in the HTML, 2) the JS functions exist and work, 3) CSS is not blocking visibility with !important, 4) PRAYER_TEXTS has section data."

---

## 🔴 ISSUE 4: FONT SIZE (A+/A-) AFFECTS EVERYTHING
**Priority: HIGH**

**The Problem:**
When pressing A+ or A- to change font size, it changes the size of EVERYTHING on the page — titles, buttons, navigation, card text, option labels — not just the prayer text.

**What Should Happen:**
- A+ and A- should ONLY change the size of `.prayer-paragraph` elements
- Optional prayers (`.optional-prayer`) should ALSO scale
- Prayer instructions should ALSO scale
- These should NOT change size:
  - Section titles/headers (h2, h3, h4)
  - Navigation buttons
  - Card text on home page
  - Easy Access Toolbar buttons
  - Footer text

**Technical Details:**
- There were TWO `adjustPrayerFont` functions defined (duplicate code). The second one at around line 270 used broad selectors like `.hebrew-text` which matched card elements too
- There was ALSO an `adjustFont` function that set `document.body.style.fontSize` which changed EVERYTHING
- The first `adjustPrayerFont` (around line 41) is the correct targeted version
- The duplicate was removed and `adjustFont` was changed to redirect to `adjustPrayerFont`
- But the fix may not have fully propagated — verify only ONE adjustPrayerFont exists and it ONLY targets prayer containers

**How to Fix in Cursor:**
> "The font size A+/A- buttons change EVERYTHING on the page instead of just prayer text. There should be only ONE adjustPrayerFont function that targets elements inside #prayer-view, #birkat-view, #meein-view, #haderech-view with class .prayer-paragraph. It should NOT use .hebrew-text selector (too broad). The adjustFont function should NOT set document.body.style.fontSize. It should only set the --prayer-font-size CSS variable."

---

## 🟠 ISSUE 5: PARCHMENT MODE NOT WORKING PROPERLY
**Priority: HIGH**

**The Problem:**
Clicking the parchment button (📜) either does nothing, or it affects the entire page (including header and home cards) instead of just the prayer text area.

**What Should Happen:**
- Parchment mode adds class `parchment-active` to `<body>`
- ONLY the prayer text container gets a parchment background
- Prayer text color changes to dark brown (#3d2c1e)
- Header, sidebar, home page, footer should NOT change
- Parchment background: cream/tan gradient (linear-gradient #f4ead5 to #e8dcc0)
- Border: 2px double #c4a775

**Technical Details:**
- `window.togglePrayerBg()` toggles `parchment-active` class on body
- CSS rules target `body.parchment-active .prayer-content-wrapper` etc.
- There's also a rule `body.parchment-active header.navbar { background: inherit !important }` that may be causing the header to lose its styling
- The `inherit` keyword may be wrong — should be `unset` or the specific header color

**How to Fix in Cursor:**
> "Parchment mode should only change the prayer text area background to a parchment/cream gradient. It should NOT affect the header, footer, home page cards, or sidebar. Check the CSS rules for body.parchment-active and make sure header.navbar keeps its original background color. The rule 'background: inherit' on the header is wrong — remove it or use the specific header color."

---

## 🟠 ISSUE 6: SUBSCRIBE BUTTON SCROLL BUG
**Priority: MEDIUM**

**The Problem:**
When clicking the Subscribe button in the footer newsletter section, the page scrolls weirdly — the header image jumps, the user ends up at a random scroll position, and the "Thank You" page may not appear correctly.

**What Should Happen:**
- User types email, clicks Subscribe
- Page smoothly transitions to the "Thank You" view
- No scroll jumping, no header movements
- Thank You view says something like "Thanks for subscribing!"

**Technical Details:**
- `window.handleSubscribe(event)` calls `e.preventDefault()` then `window.setupView('thank-you')`
- The form may be submitting before preventDefault runs
- The view transition may not scroll to top
- Add `window.scrollTo(0, 0)` after view change

**How to Fix in Cursor:**
> "When clicking Subscribe in the footer, the page jumps around instead of smoothly showing the Thank You page. Make sure e.preventDefault() fires first, then setupView('thank-you') is called, then window.scrollTo(0, 0) to scroll to the top. The form should NOT actually submit."

---

## 🟠 ISSUE 7: EASY ACCESS TOOLBAR BUTTONS TOO BIG
**Priority: MEDIUM**

**The Problem:**
The A+, A-, Align, Parchment, Width buttons in the toolbar are too large. They take too much vertical space and push the prayer text down.

**What Should Happen:**
- Buttons should be small but still tappable (min 40px touch target)
- Compact padding (6px 10px)
- Font size: 0.85rem
- Toolbar should be a thin strip, not a thick bar
- Toolbar sticks below the header (position: sticky, top: 90px)

**How to Fix in Cursor:**
> "The Easy Access Toolbar (#easy-access-toolbar) buttons are too big. Make them compact: padding 6px 10px, font-size 0.85rem. The toolbar should be a thin strip that sticks below the 90px header."

---

## 🟡 ISSUE 8: LIGHT MODE HEADER NOT NAVY
**Priority: MEDIUM**

**The Problem:**
In Light Mode, the header sometimes appears white or gray instead of the requested navy blue (#003366).

**What Should Happen:**
- Light Mode header background: #003366 (dark navy blue)
- Light Mode header text: white
- Light Mode body background: #F8FAFC (ceramic white)
- Theme toggle and buttons should be visible on navy background

**How to Fix in Cursor:**
> "In Light Mode, the header should be navy blue (#003366) with white text. Check all theme-switching CSS and JS to ensure the header background is set to #003366 when light mode is active."

---

## 🟡 ISSUE 9: THEME DOESN'T PERSIST AFTER REFRESH
**Priority: MEDIUM**

**The Problem:**
When the user selects Light Mode or Parchment Mode and refreshes the page, it reverts back to Dark Mode (the default).

**What Should Happen:**
- Save theme choice to localStorage
- On page load, read from localStorage and apply the saved theme
- Same for parchment mode — remember if it was on

**How to Fix in Cursor:**
> "Theme mode doesn't persist after page refresh. Save the current theme to localStorage when changed, and read it on page load to restore the user's preference."

---

## 🟡 ISSUE 10: BOOK ICON FAB POSITION
**Priority: MEDIUM**

**The Problem:**
The floating book icon (📖) at bottom-left is either too low (gets covered by mobile browser navigation bars) or overlaps with the section-nav FAB.

**What Should Happen:**
- Book FAB: `bottom: 130px; left: 20px`
- Section Nav FAB: `bottom: 180px; left: 25px`
- Both should have `z-index: 10000+`
- They should NOT overlap each other

---

# PART 3: FEATURES THAT WERE NEVER ADDED

These features were requested but never implemented. They need to be built from scratch.

---

## 🆕 FEATURE 1: MyZmanim Widget (Prayer Times)
**NEVER IMPLEMENTED — Was a major request**

**What Was Requested:**
- Embed the MyZmanim.com widget showing daily halachic prayer times
- Display times for: Alot HaShachar (Dawn), Netz (Sunrise), Latest Shema, Latest Shacharit, Chatzot (Midday), Mincha Gedola, Mincha Ketana, Plag HaMincha, Shkiah (Sunset), Tzait HaKochavim (Nightfall), Chatzot HaLaila (Midnight)
- Location-based (use geolocation or default to a major city)
- Scrollable container (max-height: 500px)
- Should start from Dawn/Alot at the top
- Placed in the right column of the home page

**What Exists Now:**
- There's a `zmanim.js` file in the js/ folder but it may not be connected
- The `#home-zmanim-container` div exists in the HTML
- An iframe or internal fetch was discussed but never fully wired up

**How to Tell Cursor:**
> "Add a Zmanim (prayer times) widget to the right side of the home page in the #home-zmanim-container. Use the MyZmanim.com embed or fetch zmanim data from an API like hebcal.com/zmanim. Show times for Dawn, Sunrise, Latest Shema, Latest Shacharit, Midday, Mincha Gedola, Mincha Ketana, Sunset, Nightfall, Midnight. Make it scrollable with max-height 500px. Start from Dawn at the top."

---

## 🆕 FEATURE 2: Book Opening Splash Screen
**PARTIALLY IMPLEMENTED — May not work**

**What Was Requested:**
- When the app first loads, show a brief animation of a book opening
- Elegant, premium feel
- Fades into the main app after 2-3 seconds

**How to Tell Cursor:**
> "Add a splash screen animation when the app loads. Show a book opening animation (CSS transform/animation) for 2-3 seconds, then fade out to reveal the main app."

---

## 🆕 FEATURE 3: Scroll Position Memory
**NEVER IMPLEMENTED**

**What Was Requested:**
- When the user navigates away from home (e.g., opens a prayer), then clicks Back/Home, the page should return to the same scroll position they were at before

**How to Tell Cursor:**
> "When navigating away from home view, save the current scroll position. When goHome() is called, restore that saved scroll position so the user returns to where they were."

---

## 🆕 FEATURE 4: Daily Wisdom Quote
**MAY BE PARTIALLY IMPLEMENTED**

**What Was Requested:**
- A card below the weather widget showing a rotating quote from Pirkei Avot
- Different quote each day
- Shows Hebrew text and English translation
- Source attribution (e.g., "Ben Zoma, Pirkei Avot 4:1")

---

## 🆕 FEATURE 5: Search Functionality
**MAY NOT BE WORKING**

**What Was Requested:**
- Search bar on home page
- Click to open search modal
- Type keywords to search through all prayer texts
- Show matching results
- Click result to jump to that prayer/section

---

## 🆕 FEATURE 6: Tehillim (Psalms) Section
**NEVER ADDED — Show as "Coming Soon"**

**What Was Requested:**
- A Tehillim section with selected Psalms
- Since prayers aren't ready yet, show a "Coming Soon" placeholder

---

## 🆕 FEATURE 7: Sefirat HaOmer Counter
**NEVER ADDED**

**What Was Requested:**
- During the Omer counting period (Pesach to Shavuot), show the current day count
- Include the bracha and count text

---

# PART 4: TESTING CHECKLIST

After fixing issues, test EVERY item:

- [ ] No gap between header and content on home page
- [ ] No gap between header and content on prayer views
- [ ] Clock fits entirely in header
- [ ] Clicking prayer card opens the prayer
- [ ] Clicking ☰ Jump button on card opens section modal
- [ ] Section modal lists prayer sections
- [ ] Clicking a section scrolls to it
- [ ] Floating ≡ button appears when in prayer view
- [ ] Floating ≡ button opens sections for current prayer
- [ ] A+ only increases prayer paragraph size
- [ ] A- only decreases prayer paragraph size
- [ ] Titles do NOT change size with A+/A-
- [ ] Card text on home does NOT change with A+/A-
- [ ] Parchment mode changes prayer area to cream/tan
- [ ] Parchment does NOT affect header
- [ ] Parchment does NOT affect home page cards
- [ ] Subscribe navigates to Thank You without scroll jump
- [ ] Light mode header is navy blue (#003366)
- [ ] Theme persists after page refresh
- [ ] Book FAB at 130px from bottom
- [ ] Section Nav FAB at 180px from bottom
- [ ] Zmanim widget shows prayer times
- [ ] All Hebrew text displays correctly
- [ ] Bold first word of each bracha
- [ ] Optional prayers have muted styling
- [ ] Footer sticks to bottom
- [ ] Site works on mobile screen sizes

---

# END OF GUIDE

Open this folder in **Cursor**, switch to **Agent mode** (Ctrl+L → Agent tab), and paste the issue descriptions one at a time. Let it fix each issue, preview the result, then move to the next.
