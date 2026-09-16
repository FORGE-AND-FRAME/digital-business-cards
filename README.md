# Cardify — Digital Business Card Platform

A static, multi-customer digital business card system built for free GitHub
Pages hosting. No backend, no database, no build step — just HTML, CSS and
vanilla JavaScript.

Each customer gets their own card at its own URL:

```
https://USERNAME.github.io/digital-business-cards/cards/rahul/
https://USERNAME.github.io/digital-business-cards/cards/amit/
https://USERNAME.github.io/digital-business-cards/cards/demo/
```

There is no public list of customers anywhere on the site. A card is only
reachable if you have its direct link.

---

## 1. Project structure

```
digital-business-cards/
│
├── index.html            → main marketing site (Cardify's own homepage)
├── style.css              → main site styles
├── script.js               → main site behaviour (mobile menu, etc.)
├── 404.html               → "Card Not Found" page (see section 5)
│
├── assets/
│   ├── logo.svg            → Cardify wordmark
│   ├── default-profile.jpg → fallback avatar if a card has no photo
│   ├── card.css            → shared styling for every customer card
│   └── card.js              → shared render engine for every customer card
│
└── cards/
    └── demo/
        ├── index.html       → example card (configuration + render call)
        └── profile.jpg       → example card's photo
```

`assets/card.css` and `assets/card.js` are shared by **every** customer card.
A customer's own `index.html` only holds their `cardData` configuration —
it does not repeat any markup or styling logic.

---

## 2. Important privacy note

GitHub Pages is static hosting. There is no login system and no real
access control. What this project actually gives you:

- each customer has a unique, unguessable-by-browsing URL
- there is no homepage, search page or directory that lists customers
- one customer's page never links to another customer's page

It does **not** give you password protection or true server-side privacy.
Anyone who has (or finds) a card's exact URL can open it. Don't describe
these cards as private or secure to customers — describe them as
"unlisted": not published anywhere, only shared directly.

---

## 3. Adding a new customer

1. Copy `cards/demo/` and rename the copy to the customer's ID, e.g.
   `cards/rahul/`. Use lowercase letters, numbers and hyphens only.
2. Replace `profile.jpg` inside that folder with their photo (same filename,
   or update `profileImage` in the next step to match).
3. Open the new `cards/rahul/index.html`.
4. Edit the `cardData` object near the top of the file — name, title,
   company, phone, email, socials, services, theme colors, and so on. Leave
   any field you don't need as an empty string `""` or an empty array `[]`
   and it will be hidden automatically (see section 6).
5. Update the `<title>` and `<meta name="description">` tags near the top
   of the file to match the customer.
6. Commit and push to GitHub. GitHub Pages publishes the new folder
   automatically — no build step.
7. Give the customer their URL:
   `https://USERNAME.github.io/digital-business-cards/cards/rahul/`
8. Generate their QR code by opening that URL — every card renders its own
   live QR code from its current address, so there's nothing extra to
   generate or maintain by hand.

That's the entire workflow. Nothing outside the customer's own folder needs
to change.

---

## 4. The `cardData` configuration

Every field is optional except `name`. Anything left empty is skipped —
empty social links don't render a button, an empty `services` array hides
the Services section entirely, and so on.

```js
const cardData = {
  name: "Rahul Sharma",
  title: "Founder & CEO",
  company: "XYZ Enterprises",
  bio: "Building innovative digital solutions for modern businesses.",

  phone: "+919876543210",
  whatsapp: "+919876543210",
  email: "rahul@example.com",
  website: "https://example.com",
  address: "Pune, Maharashtra, India",

  profileImage: "./profile.jpg",

  about: "A longer paragraph for the About section (optional — falls back to bio).",

  services: [
    "Web Development",
    "Digital Marketing",
    "Graphic Design"
  ],

  instagram: "https://instagram.com/",
  linkedin: "https://linkedin.com/",
  facebook: "",
  twitter: "",
  youtube: "",

  googleMaps: "https://maps.google.com/",

  theme: {
    primary: "#111111",  // cover banner base color
    accent: "#6366f1"     // buttons, links, highlights
  }
};
```

Phone and WhatsApp numbers should include the country code (e.g.
`+919876543210`) so the call and WhatsApp links work correctly everywhere.

---

## 5. "Card Not Found" page

`404.html` at the project root is GitHub Pages' built-in way of handling
any URL that doesn't exist — including a mistyped or removed card folder.
It shows a clean "Card Not Found" message with a button back to the main
site.

Because GitHub Pages can serve this file for a URL at any depth, it can't
safely use a plain relative link back to the homepage. Open `404.html` and
set the `SITE_BASE` constant near the bottom of the file to match how you
deploy:

```js
// Project site, e.g. https://USERNAME.github.io/digital-business-cards/
var SITE_BASE = "/digital-business-cards/";

// Custom domain, or a user/organization site at the domain root:
var SITE_BASE = "/";
```

---

## 6. How rendering works

Each card's `index.html` does three things:

1. Loads `assets/card.css` for styling and `assets/card.js` for the render
   engine.
2. Defines a `cardData` object with that customer's information.
3. Calls `renderCard(cardData)`, which builds the card's HTML into
   `<main id="card-root">`, wires up the Call / WhatsApp / Email / Website /
   Maps buttons, the Save Contact download, the Share button, and the QR
   code — all from that one object.

Sections only appear when their data is present:

- No `services` array (or an empty one) → Services section is omitted.
- No social URLs → no social buttons render.
- No `address` and no `googleMaps` → Location section is omitted.
- No `phone` / no `whatsapp` → that specific button is omitted.

---

## 7. Features implemented

- **Save Contact** — generates a `.vcf` file client-side (no backend) with
  name, phone, email, organization, job title and website, and triggers a
  download.
- **Share Card** — uses the Web Share API where the browser supports it;
  otherwise copies the card's URL to the clipboard and shows a small
  "Card link copied!" confirmation.
- **QR Code** — every card renders a live QR code of its own current URL
  (`window.location.href`), so it's always correct even if you move the
  card to a different path.
- **Theming** — each card sets its own accent and primary colors via CSS
  custom properties, driven entirely by `cardData.theme`.

### External dependency: QR code library

This project is otherwise fully self-contained, but generating a scannable
QR code from arbitrary text in the browser is impractical to hand-roll well.
Each card loads **qrcode.js** (by davidshimjs, MIT licensed) from the
cdnjs CDN:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
```

This is the one external script the project uses. If a card is opened
offline, the QR section falls back to showing the card's link as text
instead of failing silently.

---

## 8. Deploying to GitHub Pages

1. Create a new GitHub repository, e.g. `digital-business-cards`.
2. Push this entire folder's contents to the repository's default branch.
3. In the repository, go to **Settings → Pages**, and set the source to
   deploy from that branch (root).
4. Your site will be live at:
   `https://USERNAME.github.io/digital-business-cards/`
5. Update `SITE_BASE` in `404.html` to match your repository name (see
   section 5) if it differs from `digital-business-cards`.

No build command, environment variables, or server config is required.

---

## 9. What this project intentionally does not include

- No customer directory, search page, or "all cards" listing.
- No login system or password protection.
- No payment processing (pricing on the homepage is placeholder copy —
  edit the figures directly in `index.html`).
- No backend, database, or server-side code of any kind.
