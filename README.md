# Dual Validator

Dual Validator is an Astro site that checks one or more URLs against the W3C HTML and CSS validators, then renders the results as per-page summary cards.

The live site is deployed with GitHub Pages at `https://juan-zv.github.io/dual-validator`.

## Stack

- Astro 6
- Bun
- Vanilla browser-side JavaScript inside an `.astro` component
- W3C Nu HTML Checker
- W3C CSS Validator
- GitHub Pages deployment via GitHub Actions

## What The Page Does

The main UI lives in [src/components/Validator.astro](src/components/Validator.astro). It provides:

- A URL input area that accepts one URL per line or a comma/space separated list.
- A theme toggle that persists the preferred theme in `localStorage`.
- Sample URL and clear buttons for quick testing.
- Live result cards that update as each page finishes validation.

The validator normalizes input before sending it to the remote services. It trims surrounding punctuation, adds `https://` when a protocol is missing, and removes duplicates before any network calls start.

## Validation Flow

1. The form reads the textarea and parses the raw input into a unique list of absolute URLs.
2. Each URL is checked in parallel against the HTML validator and the CSS validator.
3. HTML results are summarized from the validator `messages` array.
4. CSS results are summarized from the top-level `cssvalidation.errors` and `cssvalidation.warnings` arrays, with counts taken from the validator summary fields.
5. The UI renders a card for each URL with overall status, counts, and short message lists.

Important implementation details in [src/components/Validator.astro](src/components/Validator.astro):

- `normalizeUrl()` cleans and canonicalizes user input.
- `parseUrls()` splits the textarea content and deduplicates URLs.
- `validateHtml()` calls `https://validator.w3.org/nu/?out=json&doc=<url>`.
- `validateCss()` calls `https://jigsaw.w3.org/css-validator/validator?output=json&uri=<url>`.
- `summarizeHtml()` separates HTML errors from warnings.
- `summarizeCss()` reads the Jigsaw JSON shape and exposes CSS counts and messages.
- `renderCard()` updates the page once each URL has been validated.

## Deployment

The project is configured for GitHub Pages using Astro’s GitHub Actions workflow.

1. Push to the `main` branch.
2. GitHub Actions runs the Astro build and deploys the site.
3. The published URL is `https://juan-zv.github.io/dual-validator`.

The deployment workflow lives in [.github/workflows/deploy.yml](.github/workflows/deploy.yml).

The Astro config sets both the site URL and the repository base path so internal links resolve correctly on GitHub Pages.

## Local Development

Run the project with Bun from the repository root:

| Command | Action |
| :-- | :-- |
| `bun install` | Install dependencies |
| `bun dev` | Start the local dev server |
| `bun build` | Build the production site |
| `bun preview` | Preview the production build locally |

## Project Structure

```text
/
├── astro.config.mjs
├── bun.lock
├── public/
├── src/
│   ├── components/
│   │   └── Validator.astro
│   ├── layouts/
│   │   └── Layout.astro
│   └── pages/
│       └── index.astro
└── .github/
	└── workflows/
		└── deploy.yml
```
