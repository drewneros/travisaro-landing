# Travisaro Landing Page — Claude Session Memory

## Stack
- Plain HTML/CSS/JS (no build step)
- Responsive design with mobile-first approach
- Third-party integrations: amCharts5 (lazy-loaded for globe visualization)

---

## Hero Carousel (Sep 21, Haiku session)

### What It Does
A three-slide carousel in the hero section that cycles automatically:
1. **Add-visa screen** — phone mockup from the app, enters from right, exits left
2. **Trip-validation screen** — different phone mockup, enters from left, exits right
3. **Interactive 3D globe** — amCharts5 library, spinnable by dragging, demo route data visible

### Key Implementation Details

**Slide positioning & transitions:**
- Three divs with class `.phone-slide` + position class (`pos-left`, `pos-right`, `pos-bottom`, `pos-center`)
- CSS defines opacity/transform for each position state
- Transitions are smooth (750ms) with 3400ms hold per slide (plus 1800ms extra on globe)
- **Critical fix**: `enter()` and `exit()` functions must clear ALL positional classes before applying the next one — stale classes can block opacity transitions and make slides permanently invisible

**Carousel loop:**
- Exit and enter fire on the same tick (overlapping crossfade), not sequentially
- This prevents a 750ms "dead air" gap where all slides become invisible between transitions
- Pauses when scrolled out of view (IntersectionObserver)
- Pauses while user actively drags the globe (don't interrupt mid-interaction)
- Respects `prefers-reduced-motion` — shows slide 1 statically, no auto-advance

**Globe rendering:**
- amCharts5 libraries are lazy-loaded in the background (non-blocking)
- Only initializes when carousel reaches slide 3
- A "Drag to explore" hint appears only if globe successfully initializes
- Fallback: immediate starfield canvas background in case CDN is blocked/slow

**Image files:**
- `mockup1.png` (1376×2034): Add-visa screen mockup
- `mockup2.png` (1376×2034): Trip-validation screen mockup
- Both cropped from original 3500×3500 files to remove transparent padding (78% of the original canvas was blank)

### Known Limitation
The amCharts globe rendering & drag interaction cannot be tested in this cloud sandbox (CDN policy blocks amCharts CDN). The Travisaro app successfully loads this same library in production, so it should work on the live landing page. **Verify manually post-deploy.**

### Files to Watch
- `index.html` — contains carousel JS (200+ lines) and all three slide markup
- `mockup1.png`, `mockup2.png` — phone mockup assets

---

## Git Practices
- Push directly to `main` (user-approved pattern, auto-deploys to the website)
- Feature branches: use `claude/` prefix (e.g., `claude/website-mobile-optimization-v98v6r`)
- Commit messages should be clear and descriptive (amCharts fallback behavior, class management fixes, etc.)

---

## PR Template
When creating PRs, include:
- **Summary**: what changed and why (root cause if bug fix)
- **Test plan**: what was verified (desktop width, mobile width, accessibility, etc.)
- **Known limitations**: what couldn't be tested in the sandbox (e.g., globe rendering if CDN blocked)
