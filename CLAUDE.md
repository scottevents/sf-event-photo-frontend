# CLAUDE.md — SF Event Photo Frontend

## Project Overview

Static multi-page website for SF Event Photo, a professional event photography business serving San Francisco and Los Angeles. Features photo galleries, e-commerce (photo licensing/purchasing), user authentication, and an admin dashboard.

## Tech Stack

- **Pure HTML/CSS/JavaScript** — no frameworks, no build tools, no bundlers
- **Static MPA** — each page is a self-contained `.html` file with embedded `<style>` and `<script>` tags
- **Backend API** — hosted at `https://sf-event-photo-backend.vercel.app` (RESTful, separate repo)
- **Cloudflare Images** — CDN for photo delivery (`imagedelivery.net`)
- **Stripe** — payment processing for photo purchases
- **PP Neue York** — custom serif font family (Thin/Light/Regular/Medium weights, WOFF2/WOFF in `fonts/`)

## Project Structure

```
├── index.html           # Homepage — project gallery grid, testimonials
├── project.html         # Individual gallery detail page (loads via ?slug= param)
├── about.html           # About page
├── services.html        # Services page
├── contact.html         # Contact form
├── search.html          # Search results (via ?q= param)
├── cart.html            # Shopping cart with license selection + Stripe checkout
├── signin.html          # Sign-in page
├── signup.html          # Registration page
├── my-photos.html       # User dashboard for purchased/downloaded photos
├── download-page.html   # Download confirmation page
├── admin.html           # Admin dashboard (project/photo management)
├── test-gallery.html    # Manual testing gallery page
├── thank-you.html       # Post-checkout thank you page
└── fonts/               # PP Neue York font files (woff2/woff)
```

## How to Run

No build step. Serve with any static file server:
```bash
python3 -m http.server 8000
# or VS Code Live Server, etc.
```

## Conventions

### Naming
- **Files:** kebab-case (`my-photos.html`, `download-page.html`)
- **CSS classes:** kebab-case (`gallery-grid`, `photo-card`, `search-container`)
- **JS variables:** camelCase (`currentFilter`, `currentSort`)
- **JS constants:** SCREAMING_SNAKE_CASE (`API_URL`, `LICENSE_INFO`)

### Code Organization
- Each HTML page is **fully self-contained** — styles in `<style>`, scripts in `<script>`
- No shared JS or CSS files — header/nav/footer HTML is duplicated across pages
- Dynamic HTML generated via template literals and `.innerHTML`
- Inline SVG icons used directly in markup

### CSS
- CSS custom properties defined in `:root` for theming:
  ```css
  --black: #1a1a1a; --white: #ffffff;
  --gray-light: #f8f8f8; --gray-mid: #e8e8e8; --gray-dark: #888888;
  --gap: 6px;
  ```
- Flexbox and CSS Grid for layouts
- Mobile-first responsive breakpoints: 600px, 768px, 1100px, 1600px

### JavaScript
- Vanilla JS with async/await for all API calls (Fetch API, no libraries)
- Global-scope functions (no modules)
- LocalStorage for client-side state:
  - `cart` — shopping cart items (JSON array)
  - `user` — current user data (JSON object)
  - `token` — JWT authentication token
  - `gridView` — user's preferred gallery view mode
  - `lastDownload` — download transaction info
- Auth via Bearer token in headers: `Authorization: Bearer ${token}`

### Typography
- Main site: **PP Neue York** (serif) — loaded via `@font-face` with `font-display: swap`
- Admin dashboard: **Inter** (sans-serif) — loaded from Google Fonts

## Key Features

- **Gallery views:** Classic, Editorial, Detailed, Masonry, Dense, Cinematic
- **Category filtering & sorting** on homepage
- **Lightbox** on project detail pages
- **Facial recognition search** on gallery pages
- **Private galleries** with password protection
- **Download codes** for free photo downloads
- **E-commerce:** license selection (Web Editorial, PR/Commercial, Client Download), Stripe checkout
- **User auth:** sign up, sign in, JWT-based, user photo dashboard
- **Admin dashboard:** project CRUD, photo upload, gallery organization

## API

All API calls go to the hardcoded base URL:
```javascript
const API_URL = 'https://sf-event-photo-backend.vercel.app';
```

Key endpoints used:
- `GET /api/projects` — list projects
- `GET /api/projects/:slug` — project detail + photos
- `GET /api/site-settings` — site-wide settings
- `POST /api/auth/login` / `POST /api/auth/register` — authentication
- `POST /api/checkout` — Stripe checkout session
- Various admin endpoints under `/api/admin/...`

## Performance

- Font preloading with `<link rel="preload">`
- CDN preconnect for `imagedelivery.net`
- `loading="lazy"` on images
- `content-visibility: auto` on gallery items
- Aspect-ratio CSS to prevent layout shift

## No Build/Test Infrastructure

- No `package.json`, no npm scripts
- No test framework or automated tests
- No linter or formatter configured
- No CI/CD pipeline in this repo

## Important Notes

- **API URL is hardcoded** in every HTML file — changing the backend URL requires updating all pages
- **Code duplication** — nav, header, footer markup repeated in every file; changes must be applied to all pages
- **No environment configuration** — no `.env`, no dev/staging/prod separation on the frontend
- The admin page (`admin.html`) uses a different font stack (Inter) than the rest of the site
