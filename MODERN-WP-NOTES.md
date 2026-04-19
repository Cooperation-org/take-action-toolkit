# Modern WordPress Development Notes

Research notes for building the Take Action Toolkit as a lightweight, modern WP plugin + block theme.

---

## The Short Version

Modern WordPress (6.5+) has changed significantly. We can build this plugin without heavy PHP, without React on the frontend, and without most of the bloat people associate with WordPress. The key new tools:

- **Block themes** (theme.json) — no PHP templates, just JSON config + HTML block markup
- **Block Bindings API** — connect core blocks to custom data without building custom blocks for every field
- **Interactivity API** — ~10KB declarative framework for frontend interactions (filtering, toggling) without shipping React to visitors
- **Custom Post Types + registered meta** — still the right data model, but now they integrate with the block editor natively
- **Script Modules** — native ES modules, deferred by default, with import maps

This means: a lightweight plugin that registers CPTs and fetches Google Calendar data, a block theme that's mostly theme.json + HTML templates, and the Interactivity API for the In-Person/Virtual filter and org category filtering. No page builders, no ACF dependency, minimal custom JS.

---

## Architecture for the Take Action Plugin

### Data Model: Custom Post Types

| CPT | Fields (registered meta) | Taxonomies |
|-----|-------------------------|------------|
| `organization` | website, email, phone, logo_url, social links (JSON), description | `org_category` (healthcare, labor, immigrant support, etc.) |
| `business` | address, phone, website, map_url, description | `business_category` |

Events come from Google Calendar — no CPT needed. The plugin fetches and caches them.

### Blocks We Need to Build

Only build custom blocks where core blocks + bindings can't do the job:

| Block | Type | Why Custom? |
|-------|------|-------------|
| `take-action/event-list` | Dynamic | Fetches cached Google Calendar data, renders event cards with date, time, location, map links. Uses Interactivity API for In-Person/Virtual filter. |
| `take-action/org-directory` | Dynamic | Queries the `organization` CPT, renders card grid with logos, descriptions, contact icons, category tags. Uses Interactivity API for category filtering. |
| `take-action/business-directory` | Dynamic | Same pattern as org directory but for businesses. Could be the same block with a `type` attribute. |
| `take-action/action-hub` | Dynamic | Renders configured action links (volunteer, donate, newsletter) as a styled section. All URLs come from plugin settings. |

Everything else (header, footer, page layout, social links, branding) is handled by the block theme + theme.json. No custom blocks needed for those.

### Google Calendar Integration

Server-side fetch + WordPress transient cache:

```
Google Calendar API (public, API key only)
    → wp_remote_get() on cron or first request
    → parse JSON, normalize event data
    → set_transient() with 15-minute TTL
    → event-list block reads from transient
```

- Public calendars only need an API key (no OAuth)
- API key stored in wp-config.php as a constant, NOT in the database
- WP-Cron job refreshes the cache every 15 minutes
- Fallback: if API fails, serve stale cache (don't show empty calendar)
- Also support iCal/ICS feed URL as a simpler alternative (no API key needed)

### Frontend Interactivity

The **Interactivity API** is perfect for our filtering needs. It's declarative (HTML directives), ~10KB runtime, server-rendered for SEO, and bundled in WP core.

Example — In-Person / Virtual filter on the event list:

```html
<div data-wp-interactive="take-action/events"
     data-wp-context='{"filter": "all"}'>

    <button data-wp-on--click="actions.setFilter"
            data-wp-class--active="context.filter === 'all'"
            data-filter="all">All</button>
    <button data-wp-on--click="actions.setFilter"
            data-wp-class--active="context.filter === 'in-person'"
            data-filter="in-person">In-Person</button>
    <button data-wp-on--click="actions.setFilter"
            data-wp-class--active="context.filter === 'virtual'"
            data-filter="virtual">Virtual</button>

    <div class="event-card"
         data-wp-bind--hidden="state.isHidden"
         data-wp-context='{"type": "in-person"}'>
        ...event content...
    </div>
</div>
```

No React shipped to the frontend. No jQuery. Just the Interactivity API runtime that WordPress already loads.

### Theme: Block Theme (theme.json)

A minimal block theme, not a classic PHP theme. Structure:

```
take-action-theme/
├── theme.json          # Colors, fonts, spacing, layout
├── style.css           # Header comment + minimal custom CSS
├── templates/
│   ├── index.html      # Homepage: event list + org directory
│   ├── page.html       # Generic page
│   └── archive-organization.html
├── parts/
│   ├── header.html     # Site logo, nav, action links
│   └── footer.html     # Social links, credits
├── patterns/
│   ├── hero.php        # "Your central hub for [City]'s activism"
│   ├── event-section.php
│   └── org-section.php
└── assets/
    └── fonts/          # Optional local fonts
```

The theme.json defines a color palette and typography that cities customize through the Site Editor — no code changes needed for branding.

---

## Onboarding: Making Setup as Easy as Possible

The goal is: a non-technical activist can go from zero to a working Take Action site with minimal steps.

### Managed Hosting Path (Easiest)

1. **Visit our setup page** → click "Create my Take Action site"
2. **Fill in a short form:**
   - City/region name
   - Google Calendar URL (public calendar they already have, or we help them create one)
   - Logo upload (or pick from defaults)
   - Primary color (color picker, or pick a preset)
   - Contact email
3. **We provision automatically:**
   - WordPress install on our bare metal server (WP-CLI scripted)
   - Theme activated with their branding applied via theme.json
   - Plugin activated with their calendar URL configured
   - Subdomain ready (e.g., `philadelphia.takeaction.org`) or custom domain
4. **They get a login** → can immediately start adding organizations and businesses via the familiar WP admin

**What we automate (WP-CLI + scripting):**
```
wp core install --url=... --title="Take Action Philadelphia" ...
wp theme activate take-action-theme
wp plugin activate take-action-toolkit
wp option update take_action_settings '{"calendar_url":"...","city":"Philadelphia","color":"#..."}'
wp user create admin their@email.com --role=administrator
```

Total time: under 5 minutes, most of it waiting for DNS.

### Self-Hosted Path

1. **Install WordPress** on any host (Bluehost, SiteGround, DigitalOcean, etc.)
2. **Install the plugin and theme** from the WP admin (upload zip or search the directory)
3. **Run the setup wizard** — the plugin launches a first-run wizard that asks the same questions as the managed form:
   - City name
   - Google Calendar URL
   - Logo
   - Colors
   - Contact info
4. **Done** — the site is live with the calendar populated and an empty org directory ready to fill in

### Setup Wizard Design (Plugin First-Run)

The plugin detects first activation and shows a wizard in the WP admin:

**Step 1 — Welcome**
"Welcome to Take Action Toolkit! Let's set up your city's activism hub in 3 steps."

**Step 2 — Your City**
- City/region name (text field)
- Tagline (pre-filled: "Your central hub for [City]'s pro-democracy activism")
- Logo upload
- Primary color picker

**Step 3 — Calendar**
- Google Calendar URL or iCal feed URL
- "Don't have one yet?" → link to instructions for creating a public Google Calendar
- Test button: "Check connection" → fetches and shows first 3 events as preview

**Step 4 — Action Links**
- Volunteer signup URL (optional)
- Donation URL (optional)
- Newsletter signup URL/embed (optional)
- Social media links (optional)

**Step 5 — Done**
"Your site is ready! Here's what to do next:"
- Add your first organization → link to CPT admin
- Share your calendar with local groups → link to instructions
- Customize your look → link to Site Editor

### Org/Business Data Entry

The WP admin for adding an organization should be dead simple:

- Title: org name
- Featured image: org logo
- Content: description (block editor, but they can just type a paragraph)
- Sidebar meta box or custom panel: website URL, email, phone, social links
- Category checkboxes: healthcare, labor, immigrant support, etc.

This is standard WordPress — anyone who's written a blog post can do this.

### Template Content

Ship the theme with starter content / patterns that show what a complete site looks like. When they activate the theme, they see a real-looking site with placeholder data, not a blank page. The setup wizard replaces the placeholders with their actual city name and calendar.

---

## Performance Approach

- **No React on the frontend** — Interactivity API only (~10KB)
- **Script modules** with `viewScriptModule` in block.json — deferred by default
- **Block-specific CSS** loaded only when the block is on the page via `wp_enqueue_block_style()`
- **Google Calendar data cached** in transients — no API calls on page load
- **Speculative loading** (WP 6.8+) — pages prefetch on hover for near-instant navigation
- **Block theme** — inherently leaner than classic themes (no template hierarchy PHP overhead)
- **No page builder dependencies** — no Elementor, no Divi, no WPBakery

---

## i18n Strategy

- All PHP strings wrapped in `__()` / `_e()` / `_x()` with `take-action-toolkit` text domain
- All JS strings use `@wordpress/i18n` with the same text domain
- block.json `textdomain` field set for automatic string extraction
- Generate POT file with `wp i18n make-pot`
- Ship with English + Spanish translations (matching Tucson's bilingual support)
- Community can contribute via translate.wordpress.org once plugin is listed

---

## Web Components: Not Needed

We investigated using native web components (Custom Elements + Shadow DOM). Conclusion: **the Interactivity API covers our needs better** for this project.

- Web components provide style encapsulation, but we *want* theme styles to apply to our components (branding, colors from theme.json)
- The Interactivity API integrates natively with the block system, server-side rendering, and WordPress hooks
- Web components would add complexity (Shadow DOM style management, React event bridging in the editor) without clear benefit for our use case
- If we later need a highly encapsulated, framework-agnostic widget (e.g., an embeddable calendar widget for non-WP sites), web components would be the right choice then

---

## What We Don't Need

Things that would add complexity without value for this project:

- **ACF / Meta Box plugins** — registered meta + Block Bindings API covers our fields
- **Page builders** — block theme + patterns handles layout
- **Custom database tables** — CPTs are fine for the data volume (~10-50 orgs per city)
- **React on the frontend** — Interactivity API handles all client-side behavior
- **Headless / decoupled architecture** — standard WordPress rendering is simpler and sufficient
- **WooCommerce** — donations are external links (Venmo, PayPal)

---

## Build Tooling

```bash
# Scaffold the plugin with multiple blocks
npx @wordpress/create-block@latest take-action-toolkit --namespace="take-action"

# Build with ES module support (needed for Interactivity API)
npx wp-scripts build --experimental-modules

# Development with hot reload
npx wp-scripts start --experimental-modules

# Generate translation template
wp i18n make-pot ./ languages/take-action-toolkit.pot
```

Plugin structure:
```
take-action-toolkit/
├── take-action-toolkit.php     # Plugin header, CPT registration, settings
├── includes/
│   ├── calendar.php            # Google Calendar fetch + cache
│   ├── post-types.php          # Organization + Business CPTs
│   ├── settings.php            # Plugin settings page + setup wizard
│   └── rest-api.php            # Custom REST endpoints if needed
├── src/
│   └── blocks/
│       ├── event-list/         # block.json, edit.js, render.php, view.js
│       ├── org-directory/
│       ├── business-directory/
│       └── action-hub/
├── build/                      # Compiled assets (gitignored)
├── languages/
│   ├── take-action-toolkit.pot
│   └── take-action-toolkit-es_ES.po
└── readme.txt                  # WordPress.org plugin directory listing
```
