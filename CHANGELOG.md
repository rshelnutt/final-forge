# Changelog

All notable changes to Final Forge will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## 2026-03-12

### Added
- Structured GitHub issue templates with YAML forms: Bug Report, Feature Request, Documentation Issue, and Card Data Issue — replacing the old Markdown templates
- Automated issue management workflows: Claude-powered triage and duplicate detection, auto-close duplicates, lock stale issues, lifecycle comments, and issue sweep
- 12 new issue labels for feature areas (binder-builder, deck-builder, collection, search, card-data, card-pricing, missing-card, etc.) and workflow automation (triage, autoclose)

---

## 2026-03-09

### Added
- Global search across cards, sets, and precons with fuzzy matching, card previews, and keyboard shortcuts
- Collection sharing now supports game switching between MTG and FF TCG
- Updated landing page with new hero section and multi-game messaging

---

## 2026-03-06 — 2026-03-08

### Added
- Social login via Google and Discord OAuth, with account linking and unlinking for existing accounts
- Major UI redesign: new sidebar-based navigation, unified dashboard layout, and top bar with integrated search
- New routing structure with dedicated binder builder and deck builder sections
- Admin panel with game settings, set management, and user administration
- Full analytics support for Final Fantasy TCG including missing cards, completion progress, and value tracking

---

## 2026-03-03 — 2026-03-05

### Added
- Animated set and game selection screen with visual transitions between MTG and FF TCG
- Dual-color flame aura effects for cards with animated fire shader and mask pathing

---

## 2026-03-01

### Added
- Privacy policy and terms of service pages (GDPR-compliant)
- Full account deletion flow with confirmation safeguard — removes all user data, collection, profile, and settings permanently

---

## 2026-02-28

### Added
- Legacy sponsor crown icon with animated twinkling sparkle effects
- Notification list read more / show less expansion
- Smokescreen build test to prevent errored deployments

### Changed
- Major light mode styling overhaul across all components including analytics, builder, cards, sidebar, and top bar
- Updated FAB navigation order on mobile

### Fixed
- Resolved critical crash caused by crown icon introduction
- Fixed several mobile analytics layout issues

---

## 2026-02-27

### Added
- Rebuilt the analytics section from the ground up with three dedicated pages: Value Insights, Completion Progress, and Missing Cards. Each page features KPI cards, interactive charts, sortable data tables, and set quick-filter badges for instant scoping.
- Added price sparklines to card displays across analytics, showing 30-day price trend mini-charts per card.
- Added trending cards sections showing the top 5 cards with the biggest price increases and decreases.
- Added a value over time line chart tracking total collection value day by day with tooltips and date formatting.
- Added a Cost to Complete KPI card with tabs to toggle between cheapest-available (nonfoil) and premium (foil/surge) pricing.
- Added set quick-filter badges across all analytics pages. Click any set to instantly filter the entire page to that set — no loading screens, no recomputation.

### Fixed
- Fixed shared collection pages showing incorrect foil and surge effects, including an old animated surge texture. Share page card rendering now matches the main collection grid exactly.
- Consolidated duplicated card variant detection logic (getHoloVariant, getFinishAvailability) into a single shared utility used by all card display components. Eliminates inconsistencies between the card grid, carousel, analytics zoom, and share pages.
- Fixed a trailing brace typo in the card grid surge overlay CSS class name.

### Changed
- Analytics pages now use a single cached data source with lightweight memo-based filtering, making set filter switching instant with no blocking or recomputation.
- Moved per-set rarity stats computation into the global analytics hook, eliminating redundant per-set recalculations on filter changes.

---

## 2026-02-19

### Added
- Added USD/EUR currency preference setting. Toggle between US Dollar and Euro for all price displays across analytics, dashboard, card detail panels, and sidebars. European format option (0,11 €) available when EUR is selected.
- Added Cardmarket as a second purchase destination alongside TCGPlayer. Buy links for Cardmarket now appear in the card carousel detail panel and card sidebar price popover.

### Changed
- Overhauled holographic card effects. Unified foil and surge foil shine layers, added back-face flip support for Golden Chocobo cards, updated card border radius for better accuracy, and improved glare fade-out behavior on pointer leave.
- Dashboard subtitle now dynamically shows "Cardmarket prices" or "TCGPlayer market prices" based on currency preference.

---

## 2026-02-17

### Added
- Added account settings with username system. Edit your profile (username, first name, last name), change your password, and display your username on shared collections instead of your email. Usernames are validated for uniqueness with a 30-day change cooldown.
- Migrated set metadata to our own database. Set names, icons, and types now load from Supabase instead of calling the Scryfall API on every page load, resulting in faster page loads and eliminating rate limit concerns.

### Fixed
- Fixed a critical issue where authenticated users' collections could be lost due to a data structure mismatch in the persistence layer.
- Fixed collection schema version tracking not persisting correctly, which could cause migrations to re-run on every page load.

### Changed
- Card carousel images now use consistent corner rounding that matches the rest of the app.

---

## 2026-02-16

### Added
- Added system broadcast mechanism that pushes "new version available" notifications to all connected clients in real-time, including guests. A persistent toast with reload/dismiss options appears when a new version is deployed.
- Notification toasts now appear automatically when real-time notifications arrive from the server, matching the notification type (info, success, or error).
- Import-complete notifications now fire for all three import flows (Pre-constructed, TCGPlayer, and Manabox/Collectr).
- Redesigned notification list with toolbar showing unread count, "mark all read" and "delete all" buttons, plus a truncation system that shows 10 notifications with a "Show more" expand option.

### Fixed
- Fixed a missing promo card (Zidane, Tantalus Thief) not appearing in the Special Promos set. The card was excluded because Scryfall hadn't tagged it with the expected Final Fantasy game identifier. Added a manual inclusion system to catch cards like this going forward.
- Fixed the "What's New" announcement toast persisting after logging out instead of being dismissed.
- Fixed the theme toggle being invisible on logged-out views.
- Fixed a visual flash during card grid page transitions caused by a visibility toggle hack.
- Fixed rapid page changes in the card grid queueing up behind the current animation. Page transitions are now interruptible for snappier navigation.

### Changed
- Redesigned the announcement toast from a vertical to horizontal layout.
- Updated mobile bottom navigation bar to use brand purple in light mode.
- Adjusted mobile set carousel drop shadow intensity for light mode.
- Updated mobile dashboard KPI row styling for light mode with softer background and shadow.

---

## 2026-02-14

### Fixed
- Resolved an issue where art cards and promo cards failed to import from Collectr CSV files. Imports now correctly match these card types by stripping set name prefixes and using name-based fallback matching.
- Fixed token card imports from Collectr failing when collector numbers were missing or incorrect. The system now falls back to matching tokens by card name across all token sets.

### Changed
- Added real-time progress feedback during collection file imports, showing the number of cards matched as the import processes.
- Updated all numeric displays throughout the app to use thousands separators (e.g., "1,234" instead of "1234") for improved readability.

---

## 2026-02-12

### Added
- Added collection file import feature supporting Manabox (CSV and TXT formats) and Collectr (CSV format). The system automatically detects the file format, matches cards against the database, removes duplicates, and shows a preview before importing. Includes mobile-optimized interface.

---

## 2026-02-10

### Added
- Added full-screen card carousel for browsing cards one at a time. Supports keyboard shortcuts (arrow keys, Enter, Shift+Tab, Space), mouse clicks, and touch swipe gestures. Includes smooth animations and displays card pricing and metadata.
- Added 3D holographic card effects that respond to mouse and touch movements. Cards tilt dynamically and display realistic foil treatments including standard foil, surge foil, and a special effect for Golden Chocobo cards. Double-faced cards can be flipped to view the back.
- Redesigned the card grid with improved interactions. Clicking a card now toggles ownership with a smooth animation. Hovering over owned cards reveals finish options (nonfoil, foil, surge). Added visual foil overlays and marketplace price integration.
- Implemented smooth animation system for rapid ownership and finish changes that prevents visual jank and reduces unnecessary server requests.

---

## 2026-02-09

### Added
- Added one-click import for 14 pre-constructed products including Commander decks, Starter Kit, Scene Boxes, and promo sets. Products already in your collection are marked with a checkmark.
- Added support for meld cards (Fang, Vanille, Ragnarok). Meld card parts can be flipped to view their meld halves, and the complete meld result can be viewed with both halves together. Includes navigation between meld partners.

---

## 2026-02-08

### Added
- Added TCGPlayer collection import via URL. Paste your TCGPlayer collection page URL to automatically scrape and import your cards. The system matches cards by name and set, detects variants (borderless, extended art, surge foil), and shows a preview before importing. Includes progress tracking and the ability to cancel during import.

---

## 2026-02-06

### Added
- Added collection analytics dashboard showing overall completion percentage, total collection value, per-set statistics, rarity breakdowns, top 20 most valuable cards, and a list of missing cards sorted by price.
- Added detailed per-set analytics pages with set-specific completion stats, rarity breakdowns, and missing card lists. Includes dropdown navigation to quickly switch between sets.

---

## 2026-02-04

### Added
- Added mobile bottom navigation bar with slide-up drawer panels for Data, Settings, and Account sections. Drawer heights adapt to content and include keyboard navigation support.
- Added special card tracking settings for Black Chocobo and Golden Chocobo cards. Choose which printing to track for completion statistics or exclude them entirely.
- Added token display mode setting to show tokens either as separate sets or combined with their parent sets.

### Fixed
- Fixed layout corruption on mobile devices when opening the virtual keyboard in fullscreen mode. The app now exits and re-enters fullscreen automatically to prevent this issue.
- Fixed mobile drawer toggle not working correctly when trying to close an open drawer by clicking the navigation icon. Drawers now close properly when clicking the same icon that opened them.
- Fixed mobile drawers overlapping the bottom navigation bar after dismissing the keyboard. Drawers now maintain correct positioning above the navigation bar.

---

## 2026-01-30

### Added
- Redesigned the marketing landing page with modern animations, updated hero section, feature highlights, and step-by-step guides.
- Added cloud sync for authenticated users. Sign up for a free account to sync your collection across all devices. Existing guest collections automatically migrate when creating an account.
- Added guest mode allowing immediate use without creating an account. Collections save locally in your browser with full feature access except cross-device sync.
- Added collection backup and restore via JSON export/import. Export your entire collection to a portable JSON file and restore it anytime. Files are generated locally without server involvement.
- Added dark mode and light mode theme options with automatic system preference detection. Theme preference saves to your browser.

### Changed
- Updated app branding with new color scheme (purple and teal), modern typography (Inter font family), and redesigned logo.
- Migrated from Next.js to TanStack Start with Vite for faster development and production builds. Bundle size reduced by 34% and build times improved significantly.

---

## Initial Release

### Added
- Added collection tracking for all 2,000+ cards across 12 Final Fantasy MTG sets including main sets (FIN, FCA, FIC), art series, tokens, and promos. Click any card to mark as owned or unowned.
- Added per-set completion statistics showing progress toward completing each set. Percentages update automatically as cards are added or removed.
- Added separate tracking for foil and non-foil finishes. Each card can be marked as nonfoil, foil, or surge foil independently.
- Implemented responsive design that adapts to desktop, tablet, and mobile devices with optimized layouts and controls for each screen size.
- Integrated Scryfall API for card data, high-resolution images, and market pricing. All card information updates daily.
- Added visual card grid for browsing sets. Grid displays cards in collector number order with filters for ownership status and rarity.
- Added email/password authentication via Supabase. Create an account to enable cloud sync or continue using guest mode.

### Technical
- Implemented local persistence using TanStack Query with localStorage for guest mode collections.
- Set up Supabase PostgreSQL database for user authentication and cloud-synced collections with row-level security.
- Built state management entirely with TanStack Query for both server and client state with automatic caching and optimistic updates.
- Integrated Shadcn/ui component library built on Radix UI primitives with TailwindCSS styling.
- Configured TanStack Router for file-based routing with type-safe navigation and automatic code splitting.
- Built on TanStack Start framework with Vite, React 19, and TypeScript in strict mode.
- Configured pnpm as package manager for faster installs and reduced disk usage.
