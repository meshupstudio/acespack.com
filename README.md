# acespack.com — ACES, American Compactor Equipment Sales

Static rebuild of [www.acespack.com](https://www.acespack.com), migrated off Duda.
ACES rents and sells trash compactors and balers in Central California (Fresno, Clovis, and the Central Valley).

This is plain HTML, CSS and JS. There's no framework, no build step, and no npm. It's hosted on Netlify.

## Project structure

```
.
├── index.html              Home
├── compactors/index.html   Trash Compactors        (/compactors/)
├── balers/index.html       Balers: vertical + horizontal (/balers/, #vertical-balers, #horizontal-balers)
├── about/index.html        About                   (/about/)
├── contact-us/index.html   Contact + map           (/contact-us/)
├── 404.html                Not-found page (Netlify serves it automatically)
├── assets/
│   ├── css/styles.css      The only stylesheet (brand colors/fonts as CSS custom properties at the top)
│   ├── js/main.js          Mobile nav, scroll reveal, contact-form submit, footer year
│   └── img/                Logo, favicon, real equipment photos, letters/ (scanned reference letters)
├── netlify.toml            Publish dir, cache headers, redirects from old Duda URLs
├── robots.txt, sitemap.xml
└── README.md
```

The page URLs match the old Duda site (`/about`, `/compactors`, `/balers`, `/contact-us`), so existing links and search results keep working.

## Updating content

Every page is a standalone HTML file. The **header, footer, "Why us", testimonials and contact form are repeated on each page**, so when you change one of them, make the same edit in all five `index.html` files. A find-and-replace across the project works well for this.

| What | Where |
| --- | --- |
| Phone / toll-free / fax / email | Top bar and header, footer, and the `#contact` section on every page. Search for `324-9930`, `431-2237`, `324-9931`, `info@acespack.com`. |
| Work hours | Footer "Work Hours" and the `.hours` table in the `#contact` section (every page), plus `openingHoursSpecification` in the JSON-LD block in `index.html`. |
| Mailing address | `#contact` section and footer (every page), hero on `contact-us/index.html`, JSON-LD in `index.html`. |
| Map location | `contact-us/index.html`: change the `q=` in the Google Maps `<iframe src>` (e.g. `q=123+Main+St,+Clovis,+CA+93612`). |
| Equipment applications / materials lists | The `<ul class="tags">` and `<ul class="check-list">` lists on `compactors/` and `balers/`. |
| Testimonial letters | Put an image in `assets/img/letters/` and copy one of the `<a class="letter">` blocks in the `#testimonials` section. |
| Photos | Put a web-sized JPG (about 1200–1600px wide) in `assets/img/` and update the `src`, `width`, `height` and `alt` attributes. |
| Colors / fonts | The `:root` variables at the top of `assets/css/styles.css`. |
| Page titles / meta descriptions | `<title>` and `<meta name="description">` in each page's `<head>`. |

### Brand reference

- **Colors** (from the Duda page CSS `--color_N` values and the logo): red `#ED1922` (logo), bright red `#FB0000`, pink `#F99999`, near-black `#221E1F`, cream `#FFFDFB`, green `#00B664`. Green is used for the form's success message.
- **Fonts:** Poppins (headings) and Roboto (body), loaded from Google Fonts. These are the two fonts the Duda pages used most.

## Contact form (Netlify Forms)

The form is named `contact` and appears on every page. Netlify finds it at deploy time because it carries `data-netlify="true"` in the static HTML.
`main.js` intercepts the submit (`preventDefault` followed by `fetch('/', …)`), so the visitor stays on the page and sees an inline confirmation instead of Netlify's generic success page. A hidden `bot-field` honeypot filters out spam bots.

Submissions show up in the Netlify dashboard under **Forms**. To have them emailed to you, add a notification there (Forms → Form notifications → Email).

## Local preview

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000>. The site uses root-relative paths (`/assets/...`), so you need to serve it from the repo root. Double-clicking the HTML files won't load the assets. The contact form only works on Netlify: locally, the POST returns an error and you'll see the inline error message.

## Deploying to Netlify

**Option A: Git-connected (recommended).** Every push redeploys automatically.

1. Netlify dashboard → **Add new project → Import an existing project → GitHub**, then choose `meshupstudio/acespack.com`.
2. Branch to deploy: `main` (or whichever branch is your production branch).
3. Leave **Build command** empty and set **Publish directory** to `.`. `netlify.toml` already sets both.
4. Deploy. Then set up the custom domain: **Domain management → Add a domain → `acespack.com` / `www.acespack.com`**, and update DNS at your registrar (or move it to Netlify DNS). HTTPS is provisioned automatically.

**Option B: drag-and-drop.** This is a manual deploy with no Git.

1. Netlify dashboard → **Add new project → Deploy manually**.
2. Drag the whole project folder (the one that contains `index.html` and `netlify.toml`) onto the drop zone.
3. To update later, open the project's **Deploys** tab and drag the folder in again.

Netlify Forms works with both options, as long as the form HTML is in the deployed files.

## Known gaps / TODO

- **No street address.** The Duda export only has a PO Box (Clovis). The map on `/contact-us/` shows Fresno, CA (city-level) as the service area; swap in a street address if there is a location customers should visit.
- **No horizontal baler photo.** The old site reused a compactor photo on the home page's "Horizontal Balers" card. That's kept for now, with a `TODO` comment in the HTML.
- **No social / review links** (Facebook, Yelp, etc.) were found in the export.
- The Duda "About us" intro was leftover template copy about a dentist's office, so it was dropped. Only the real "Our history" text is used.
- Typos fixed from the old site: "Toll-Fee" became "Toll-free", "Authorize Distributor" became "Authorized Distributor", "Non-ferris" became "Non-ferrous", and a truncated "applicatio" became "application".
