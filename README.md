# Ashford Capital — website

A static, framework-free website (HTML, CSS, vanilla JS). Everything the browser needs is in `site/`. Upload that folder to any static host (Netlify, Vercel, Cloudflare Pages, GitHub Pages, S3, an ordinary web server).

```
site/               ← the finished, deployable website (open site/index.html)
src/                ← source: pages, partials, CSS, JS, data, fonts
build.mjs           ← build script (Node 18+): src/ → site/
src/visuals.mjs     ← icon, illustration and figure generator (inline SVG)
package.json
```

## Before it goes live: settings

Open `site/assets/js/config.js` (source: `src/assets/config.js`):

```js
window.ASHFORD_CONFIG = {
  formEndpoint: "",   // e.g. a Formspree / Getform / Basin / your own endpoint URL
  contactEmail: "",   // e.g. "hello@yourdomain.com", shown on the Contact page when set
  social: {           // the three profiles in the Home page "Social" section
    instagram: "https://www.instagram.com/ashfordcapital_/",
    substack: "https://substack.com/@ashfordcapital",
    linkedin: ""      // set the final LinkedIn company-page URL here
  }
};
```

* **`formEndpoint`**: the readiness assessment, the contact form and the Home "Oh, hello." email form
  `POST` JSON to this URL (`{ type: "assessment" | "contact" | "join", submittedAt, page, answers | fields }`).
  Any service that accepts a JSON POST works. **While it is empty, the forms still validate and show the
  result, but they display a visible "Form delivery is not connected" notice and nothing is sent.** This is
  deliberate, so that no enquiry is silently lost. Set the endpoint and the notice disappears.
* **`contactEmail`**: left blank on purpose; no contact details were supplied. The line is hidden until set.
* **`social`**: Instagram and Substack links were taken from the handles visible in the screenshots
  (`@ashfordcapital_`, `@ashfordcapital`). The screenshot of LinkedIn shows no URL, so its card links to a
  LinkedIn search for "Ashford Capital" until you set `social.linkedin`. A value you set here overrides the
  link written in `src/pages/index.html`.

## Running locally

Open `site/index.html` directly, or serve the folder:

```
cd site && python3 -m http.server 8000      # then visit http://localhost:8000
```

(Opened straight from disk, browsers may log harmless warnings about font *preload* hints; the fonts still load.)

## Editing and rebuilding

```
npm install      # one-off; installs esbuild (the fonts are already bundled in src/assets/fonts)
npm run build    # or: node build.mjs — regenerates site/
```

* **Page copy:** `src/pages/*.html` (each starts with a `<!--meta … -->` block for title, description and which nav item is current).
* **Header / footer:** `src/partials/`.
* **Styles:** `src/css/*.css` (concatenated in filename order). Colour, type, radius and spacing tokens are at the top of `00-base.css`.
* **Behaviour:** `src/js/main.js`.
* **Icons, illustrations, figures:** drawn in `src/visuals.mjs` and placed in pages with build tokens:
  `{{ic:name}}` / `{{ic:name:sm}}` (line icon), `{{art:about|method|pathways|intelligence|begin|contact}}`
  (page-hero illustration), `{{fig:readers|operators|thinkers|hesitation|conflict|inconsistent|clarity|nosnap}}`
  (card figure), `{{node}}` (the drawn decision node used by every key insight), `{{social:instagram|linkedin|substack}}`
  (logo glyphs), `{{welcome}}` (the walking-figure scene on the Join panel, generated in `build.mjs`) and `{{voices}}` (the Home testimonial bento). Styling lives in `src/css/04-visual.css`. To add an icon, add its paths to `ICON_BODIES` in `visuals.mjs`.
  Icon accent colour is `--vi-a`; icons inherit `currentColor` and adapt on crimson sections automatically.
* **Research Library entries:** `src/data/library.json`. Add an object with `title`, `type` (shown as the
  small label, e.g. Concept, Framework), `category` + `categoryLabel` (`market-intelligence` |
  `decision-execution` | `operator-standards`), `topic` + `topicLabel` (`frameworks` | `intelligence` |
  `behaviour` | `labs` | `standards`), `summary`, and `body` (a list of `{ "text": "…" }` paragraphs and
  `{ "items": ["…"] }` bullet lists). Rebuild and the list, search, filters and topic counts update.
* **Social screenshots:** `src/assets/img/social-*.{jpg,png}`. Replace a file (keep its name) to update the picture.
* **Videos (Home, About section):** three boxes play your own videos. Put the files in `src/assets/video/`
  named `ashford-1.mp4`, `ashford-2.mp4` and `ashford-3.mp4`, then run `node build.mjs` (or copy them straight into
  `site/assets/video/` on the server). Nothing else needs editing: a box shows a "Coming soon" placeholder until
  its file exists, then swaps to a normal player (play, pause, volume, fullscreen), and starting one video pauses the others.
  To use different names or a `.webm`, change the `src` / `type` of that box's `<source>` in `src/pages/index.html`.
  Optional: add `poster="assets/video/ashford-1.jpg"` to a `<video>` for a cover image, and
  `style="--vbox-ratio: 9 / 16"` on the `.vgrid` div for vertical videos. Add a captions file with
  `<track kind="captions" src="..." srclang="en" label="English">` for accessibility. Until files exist the browser
  console logs three harmless 404s for the missing videos.
* **Client testimonials (Home, "Proof, not promises"):** four tiles, each a client photo with their words on a
  slightly dark panel so the text stays readable. Edit `src/data/voices.json` (up to four entries):
  `{ "quote": "…", "name": "Full name", "context": "Pathway, year", "image": "assets/img/voices/priya.jpg", "alt": "Priya, a Market Operators client", "focus": "50% 30%" }`.
  Put photos in `src/assets/img/voices/`; `focus` is the CSS `object-position` (where the face sits when the photo is cropped).
  **The four entries shipped here are placeholders**: silhouette images, generic wording, "Client name", and
  `"sample": true`, which prints a "Sample" badge on the tile. Replace each with a real, permissioned client quote and
  delete the `sample` line so the badge disappears. No client words were invented.
* **Method demos:** the Trade Autopsy cases are an inline JSON block in `src/pages/method.html`; the prioritization demo and decision matrix are plain markup in the same file.

## What changed in v4

* **Hero:** navbar unchanged; the title "From market knowledge to clear execution." on one line at every screen width
  (it scales to fit, so on a phone it is smaller); the sub-line, both buttons, the animated field and the floating
  cards were removed.
* **Statement panel:** only "We turn what traders know into what they do." The 4 / 3 / 0 numbers were removed.
* **About:** the inline picture is gone and the statement reflows; the three text columns were replaced by three
  video boxes (see Videos above). The insight and the "Our belief" button are unchanged.
* **Services:** the three pathway rows are burgundy, and they open when the pointer hovers (on touch screens, tap).
  Keyboard users still open a row with Enter / Space.
* **Testimonials:** same bento layout; each tile is now a client photo with the words on a dark panel (placeholders for now).
* **Join:** the left side is a small figure walking to the Ashford emblem, with "Welcome to Ashford Capital."

## Design system and page rules

The site follows `website_idea.pdf` (section order and reference images) with Ashford's own palette
(about 70% white, 25% crimson `#680C20`, 5% beige).

* **Mobile first.** Every rule in `src/css/` is written for a phone and widened with `min-width` queries.
* **No hard page edges.** Each `.sec` carries a soft blurred halo above and below, crimson areas are inset
  rounded `.panel`s, the footer fades up from the
  last section, and page-to-page navigation cross-fades (`@view-transition`).
* **Indirect titles, no kickers.** Section titles say what the reader gets ("What we have for you"), and
  there are no small page-name or page-number labels above them.
* **One key insight per section**, always the `.insight` component (tinted tile, serif italic, drawn node;
  stat and graphic variants), inside the same section.
* **Under 100 words per section**, counting title and insight. Forms, the Research Library data and
  collapsed legal clauses are counted separately (each clause is its own section).
* **Last section:** the footer is kept as it was.

## What to review before launch

* **Legal pages** (`privacy`, `terms`, `risk`, `disclaimer`) are drafts marked "pending legal review".
  The structure document is explicit that a disclaimer alone does not resolve regulatory obligations;
  the wording must reflect Ashford's actual services and applicable rules. The Privacy Policy says no
  advertising cookies are used — update it if you add analytics or ads.
* **Illustrative content.** The worked scenario, Decision Lab, Trade Autopsy, prioritization demo and
  the Research Library entries are written to demonstrate Ashford's reasoning and are labelled as
  illustrative. Replace or extend them with Ashford's own material.
* **Pathway detail** (how each pathway works, progress indicators, readiness criteria) is drawn from the
  brand document; confirm it matches what is actually delivered.
* **Testimonials / case studies** are intentionally absent: the structure document says to add them
  only when genuine, verified evidence exists (see the testimonials note above): the tiles on Home are labelled
  samples until you replace them. No pricing, session times or contact details were invented.
* **Social screenshots** are static images taken from the supplied PDF, so they are low resolution and will
  not update when the profiles change; replace them with fresh captures when you can.

## Accessibility and behaviour notes

* Works without JavaScript for reading (accordions, tabs and the method loop expand in place); the two
  forms require JavaScript and say so.
* Keyboard operable throughout; visible focus rings; `prefers-reduced-motion` respected; hover reveals
  have tap/click equivalents on touch devices; 44px minimum tap targets.
* Automated axe-core audit (WCAG 2.0/2.1 A and AA plus best practices): no violations on any page at
  390px and 1440px.
