# Take Action Toolkit — Onboarding Design

A guided onboarding flow for new Take Action locations. A location could be a city (Take Action Tucson), a state (Take Action Pennsylvania), a county, a region, or any other locality.

---

## Design Principles

1. **Start useful immediately** — the site should look real and functional from minute one, not a blank page waiting for content
2. **Don't ask for everything upfront** — get the minimum to launch, prompt for more later
3. **Meet them where they are** — many locations already have a Google Calendar, a list of orgs, social media. Connect to what exists.
4. **Progressive disclosure** — show easy steps first, reveal advanced options as they grow
5. **No jargon** — "Your event calendar" not "Google Calendar API integration"

---

## The Two Paths

### Path A: Managed (We Host It)

They never touch WordPress directly during setup. They fill out a form on our marketing site, we provision everything behind the scenes.

### Path B: Self-Hosted

They install WordPress + our plugin/theme themselves (or a volunteer does). The plugin's built-in wizard guides them through configuration.

Both paths ask the same questions and produce the same result. The difference is who handles the WordPress install.

---

## Pre-Setup: The Landing Page

Before either path, there's a public page (on takeactiontoolkit.org or similar) that explains the toolkit and sets expectations.

**What it communicates:**
- "Launch your own Take Action site in under 30 minutes"
- Show a live demo site they can click through
- "Here's what you'll need" checklist (so they can gather materials before starting):
  - [ ] Your location name and tagline
  - [ ] A logo or image (or we'll generate a simple one)
  - [ ] A Google Calendar with your events (or we'll help you create one)
  - [ ] A list of 3-5 organizations to feature (name, website, short description)
  - [ ] Links to your social media accounts
  - [ ] (Optional) Donation link, volunteer signup link, newsletter signup
- Two buttons: **"Set me up (hosted)"** / **"I'll install it myself"**

---

## Guided Setup Wizard

### Step 1: Your Location

**What we ask:**
- Location name (free text): "Tucson", "Pennsylvania", "Western Mass", "The Bronx"
- Location type (dropdown, optional): City / State / County / Region / Neighborhood / Other
- State (dropdown, for US locations)

**What we do with it:**
- Generate the site title: "Take Action [Location]"
- Pre-fill the tagline: "Your central hub for [Location]'s pro-democracy activism"
- They can edit both — some may want "Take Action Western Mass" or "Take Action 19th District"

**UX note:** The preview panel on the right updates live as they type, showing how the site header will look.

---

### Step 2: Your Look

**What we ask:**
- Logo upload (drag-and-drop, or click to browse)
  - "Don't have a logo?" → auto-generate a simple text logo with their location name in their chosen color
- Primary color (color picker with presets)
  - Presets labeled by vibe: "Bold Red", "Community Blue", "Desert Gold", "Forest Green", "Purple Power"
  - Or pick any color
- (Optional) Secondary color — defaults to a calculated complement

**What we do with it:**
- Apply to theme.json color palette
- Generate the color scheme for buttons, links, headers, cards
- Show live preview updating in real time

**Keep it simple:** Two colors + a logo is enough to make every site feel distinct. Don't offer font choices upfront — the default is good. Font customization is available later through the Site Editor for those who want it.

---

### Step 3: Your Calendar

This is the most important step — the calendar is the core feature.

**Option A: "I already have a Google Calendar"**
- Paste the calendar URL or calendar ID
- "Test connection" button → fetches and shows the next 3 upcoming events as a preview
- If it works: "Found 12 upcoming events. Looking good!"
- If it fails: friendly error with specific fix instructions:
  - "This calendar appears to be private. Here's how to make it public: [link to 3-step guide with screenshots]"
  - "That doesn't look like a Google Calendar URL. It should look like: abc123@group.calendar.google.com"

**Option B: "I don't have a calendar yet"**
- "No problem! Let's create one."
- Step-by-step inline guide (expandable, with screenshots):
  1. Go to calendar.google.com
  2. Click + next to "Other calendars" → "Create new calendar"
  3. Name it "Take Action [Location] Events"
  4. Under Settings → Access permissions, check "Make available to public"
  5. Copy the Calendar ID from the "Integrate calendar" section
  6. Paste it here
- "I've done it" → they paste the ID → we test it

**Option C: "I use a different calendar"**
- Accept any iCal/ICS feed URL (Outlook, Apple Calendar, etc.)
- Note: "Google Calendar gives you the richest event display (locations, descriptions, RSVP links). Other calendars work but may show less detail."

---

### Step 4: Your Organizations (Optional at Setup)

**What we ask:**
- "Add organizations that are active in your area. You can always add more later."
- Simple repeatable form:
  - Organization name
  - Website URL
  - Short description (1-2 sentences)
  - Category (checkboxes): Healthcare / Labor / Immigrant Support / Environment / Education / Local Resistance / Mutual Aid / Politics / Veterans / Faith / Arts & Culture / Other
- "Add another" button
- Minimum: 0 (they can skip this entirely)
- "I have a spreadsheet" → upload CSV option. Columns: name, website, description, categories. We parse and preview before importing.

**If they skip:** The org directory section shows a friendly placeholder: "Organizations coming soon — know a group that should be listed? Email [contact]" with a link to the admin to add orgs later.

**If they add 1-3:** Good enough to launch. The directory looks real.

---

### Step 5: Your Action Links (Optional at Setup)

**What we ask:**
- "Where can people take action? Add links to the ways people can get involved."
- Each is optional, with helper text:
  - **Volunteer signup URL** — "Link to a Google Form, SignUpGenius, GetZelos, or any signup page"
  - **Donation link** — "Venmo, PayPal, GoFundMe, ActBlue, or any donation page"
  - **Newsletter signup** — "Mailchimp, HubSpot, Substack, or any signup form. Paste a URL or an embed code."
  - **Email contact** — "An email address people can reach you at"
- Social media links (icon row, fill in what you have):
  - Instagram, Facebook, Bluesky, TikTok, X/Twitter, YouTube, Threads

**If they skip:** The action hub shows just the social links they provided, or a minimal "Get involved — email us at [contact]" if they gave nothing.

---

### Step 6: Review & Launch

**What they see:**
- Full preview of their site with all the settings applied
- Checklist summary:
  - ✅ Site name: Take Action Philadelphia
  - ✅ Calendar: connected, 12 upcoming events
  - ✅ Organizations: 4 listed
  - ✅ Volunteer signup: linked
  - ⬜ Donation link: not set (add later)
  - ⬜ Newsletter: not set (add later)
- **"Launch my site"** button

**After launch (managed path):**
- "Your site is live at philadelphia.takeaction.org!"
- "Want to use your own domain? Here's how: [instructions for DNS setup]"
- Email sent with their login credentials and a quick-start guide

**After launch (self-hosted path):**
- "Your site is configured! Visit [site URL] to see it live."
- Dashboard prompt with next steps (see below)

---

## Post-Setup: Guided Next Steps

After the initial wizard, the WP admin dashboard shows a "Getting Started" panel that tracks progress and suggests next steps. This persists until they dismiss it.

### The Dashboard Card

```
┌──────────────────────────────────────────────────┐
│  🎉 Your Take Action site is live!               │
│                                                    │
│  Here's what to do next:                          │
│                                                    │
│  ✅ Set up your calendar          [Connected]      │
│  ✅ Added 4 organizations         [Edit]           │
│  ⬜ Add local businesses          [Add your first] │
│  ⬜ Set up your donation link     [Add now]        │
│  ⬜ Set up your newsletter        [Add now]        │
│  ⬜ Share your site!              [Get share kit]  │
│  ⬜ Add your first blog post      [Write one]      │
│                                                    │
│  Need help? → [Documentation] [Email support]     │
│                                                    │
│                              [Dismiss this panel] │
└──────────────────────────────────────────────────┘
```

Each incomplete item links directly to the relevant admin page or settings section. No hunting through menus.

### The "Share Kit"

When they click "Get share kit," generate:
- A social media announcement they can copy/paste: "We just launched Take Action [Location]! Find events, organizations, and ways to get involved: [URL]"
- A simple flyer (PDF) with QR code pointing to the site, suitable for printing and handing out at events
- An email template to send to partner organizations: "We've listed your organization on Take Action [Location]. Check your listing and let us know if anything needs updating."

---

## Ongoing Guidance: Contextual Prompts

Beyond the dashboard card, show contextual prompts where they're relevant:

**When they visit the Organizations list and it's sparse:**
> "Tip: Most active Take Action sites feature 8-15 organizations. You have 4. Know more groups? [Add one now]"

**When they haven't posted a blog post in 30 days:**
> "Tip: A quick weekly update keeps your community engaged. Even a one-paragraph 'this week's actions' post helps. [Write a quick update]"

**When a new feature is available (we ship an update):**
> "New: You can now add business listings to support local pro-democracy businesses. [Set up your business directory]"

**When their calendar has no events in the next 7 days:**
> "Heads up: Your calendar shows no events this week. If that's intentional, great! If not, check that your Google Calendar is still connected. [Check calendar settings]"

These prompts are dismissible and non-blocking. They appear as admin notices, not modals.

---

## The CSV/Spreadsheet Import

Since activists often maintain their org lists in spreadsheets already, support importing from CSV:

**Expected columns:**
| Column | Required | Example |
|--------|----------|---------|
| name | Yes | "Indivisible Tucson" |
| website | No | "https://indivisibletucson.org" |
| description | No | "Local chapter of Indivisible focused on..." |
| email | No | "info@indivisibletucson.org" |
| categories | No | "politics, local resistance" (comma-separated) |
| phone | No | "(520) 555-0100" |
| instagram | No | "indivisibletucson" |
| facebook | No | "indivisibletucson" |

**Import flow:**
1. Upload CSV or paste Google Sheets URL (published as CSV)
2. Column mapping screen: "Which column is the organization name?" (auto-detected with preview)
3. Preview: show a table of what will be imported, flag any issues (missing names, malformed URLs)
4. "Import 12 organizations" button
5. Creates the CPT posts with meta fields populated

**Template spreadsheet:** Provide a downloadable CSV template and a Google Sheets template they can copy, pre-filled with 2-3 example rows.

---

## Domain Setup Guide

For managed hosting, they start on a subdomain. When they're ready for their own domain:

**In the Take Action settings panel:**
> "Your site is currently at philadelphia.takeaction.org. Want your own domain like takeactionphilly.org?"
>
> **Step 1:** Buy a domain from any registrar (Namecheap, Google Domains, GoDaddy, etc.)
> **Step 2:** Add these DNS records:
> - Type: A, Name: @, Value: [our server IP]
> - Type: CNAME, Name: www, Value: philadelphia.takeaction.org
> **Step 3:** Click "I've updated my DNS" → we verify and provision SSL automatically

For self-hosted, this isn't needed — they already have their own domain.

---

## What We Ship as Starter Content

When the theme activates, the site comes pre-populated with:

- **Homepage** — hero section with "[Location]" placeholder, event list block, org directory block, action hub block. All wired up and showing real data from their calendar (or placeholder events if no calendar yet).
- **About page** — template with placeholder text: "Take Action [Location] is a community hub connecting activists, organizations, and events in [Location]. We believe in..." Editable in the block editor.
- **Get Involved page** — volunteer form embed, donation link, newsletter signup, social links. All from their configured action links.
- **2-3 sample organizations** — clearly marked as examples ("Example: Replace this with a real organization") so the directory doesn't look empty but also doesn't look fake.
- **Header** — site logo, nav menu (Calendar, Organizations, Get Involved, About)
- **Footer** — social links, contact email, "Powered by Take Action Toolkit"

The setup wizard replaces placeholders with real content. Anything left as placeholder has an obvious "Edit this" prompt in the admin.
