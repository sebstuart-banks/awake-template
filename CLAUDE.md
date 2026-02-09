# CLAUDE.md - Awake Template

## Project Overview

Awake is a Nuxt.js v2 static site generator template for blogs with Netlify CMS integration. It generates a static site from Markdown content with YAML frontmatter, using Bulma/Buefy for styling and Vue.js 2.x components.

**Author:** Daniel Kelly | **License:** MIT

## Tech Stack

- **Framework:** Nuxt.js 2.x (universal/SSG mode) with Vue.js 2.x
- **Styling:** Bulma CSS + Buefy components + SCSS
- **CMS:** Netlify CMS (git-backed, admin at `/admin`)
- **Content:** Markdown files with YAML frontmatter, parsed by gray-matter and markdown-it
- **Images:** Sharp + nuxt-responsive-loader + opti-image for responsive optimization
- **State:** Vuex store for page-level data
- **Deployment:** Netlify (static generation to `dist/`)
- **Node version:** 11 (per netlify.toml)

## Commands

```bash
npm run dev          # Start dev server (runs before-build hook, then nuxt -o, then after-build hook)
npm run build        # Build for production (before-build + nuxt build + after-build)
npm run generate     # Generate static site (before-build + nuxt generate + after-build)
npm run start        # Start production server
npm run lint         # Run ESLint on .js and .vue files
```

The `before-build` and `after-build` hooks run CMS-specific scripts that generate a static JSON API from markdown content.

## Directory Structure

```
awake-template/
├── assets/
│   ├── scss/                  # Global styles (entry: styles.scss)
│   │   ├── _vars.scss         # Bulma color overrides, custom variables
│   │   ├── _layout.scss       # Layout utilities
│   │   ├── _animations.scss   # Keyframe animations
│   │   ├── _transitions.scss  # Page/element transitions
│   │   └── _util.scss         # Utility classes
│   └── uploads/               # User-uploaded media (via CMS)
├── build-tools/               # Build lifecycle scripts
│   ├── beforeBuild.js         # Pre-build hook (generates JSON API from markdown)
│   └── afterBuild.js          # Post-build hook
├── cms/
│   └── netlify/               # Netlify CMS driver
│       ├── cms.js             # CMS class definition
│       ├── post.js            # Post resource
│       ├── category.js        # Category resource
│       ├── mixins.js          # Data fetching Vue mixins
│       ├── helper.js          # CMS utilities
│       ├── hooks/             # CMS build hooks
│       ├── build/             # CMS build utilities
│       └── static/admin/      # CMS admin interface (config.yml + index.html)
├── components/                # Vue components (~25 total)
│   ├── cards/                 # Card components (PostCard, CategoryCard, GenericCard, QuoteCard)
│   └── grids/                 # Grid components (PostsGrid, CategoriesGrid, ResourceGrid, PresentationalGrid)
├── config/                    # Nuxt configuration modules
│   ├── _siteConfig.js         # Main site settings (name, theme, layout, integrations)
│   ├── modules.js             # Nuxt module registrations
│   ├── plugins.js             # Plugin list
│   ├── build.js               # Webpack/build config (gray-matter loader, PurgeCSS)
│   ├── css.js                 # CSS entry points
│   ├── head.js                # HTML head/meta configuration
│   └── generate.js            # Static route generation mapping
├── content/                   # Markdown content (git-managed)
│   ├── posts/                 # Blog posts (.md with frontmatter)
│   └── categories/            # Categories (.md with frontmatter)
├── layouts/                   # Nuxt page layouts
│   ├── default.vue            # Main layout (nav + footer)
│   └── full.vue               # Full-width empty layout
├── pages/                     # Nuxt pages (file-based routing)
│   ├── index.vue              # Home page (/)
│   ├── about.vue              # About page (/about)
│   ├── contact.vue            # Contact page (/contact)
│   ├── _singlePost.vue        # Dynamic post route (/:slug)
│   └── categories/
│       ├── index.vue          # Category listing (/categories)
│       └── _single.vue        # Single category (/categories/:slug)
├── plugins/                   # Vue plugins (auto-loaded by Nuxt)
│   ├── Globals.js             # Injects $siteConfig and $cms
│   ├── Components.js          # Registers global components
│   ├── Disqus.js              # Vue-Disqus integration
│   ├── EventBus.js            # $eventBus for sibling communication
│   └── OptiImage.js           # Responsive image plugin
├── static/                    # Static assets (copied verbatim to dist/)
├── store/
│   └── index.js               # Vuex store (page state: title, subtitle, content, etc.)
└── nuxt.config.js             # Main Nuxt config (imports from config/)
```

## Content Format

**Posts** (`content/posts/*.md`):
```yaml
---
title: Post Title
subtitle: Post Subtitle
category:
  - Category Name
author: Author Name
date: 2019-08-03T19:59:59.000Z
featureImage: /uploads/image.jpg
---
Markdown body content here.
```

**Categories** (`content/categories/*.md`):
```yaml
---
name: Category Name
image: /logo.svg
---
Category description in markdown.
```

Content is managed via Netlify CMS at `/admin` or by editing markdown files directly.

## Code Conventions

### Formatting (enforced by ESLint + Prettier)
- **No semicolons** (`"semi": false`)
- **Single quotes** (`'singleQuote': true`)
- **Arrow parens always** (`(x) => x`, not `x => x`)
- **No trailing commas** (Prettier default)

### ESLint Rules
- `no-console`: off (console.log allowed)
- `vue/no-v-html`: off (v-html permitted)
- `vue/html-self-closing`: off
- `nuxt/no-timing-in-fetch-data`: off

### Naming Conventions
- **Vue components:** PascalCase filenames (e.g., `SiteNav.vue`, `PostCard.vue`)
- **Pages:** lowercase for static routes (`about.vue`), underscore prefix for dynamic (`_singlePost.vue`)
- **SCSS partials:** underscore prefix (`_vars.scss`, `_layout.scss`)
- **Config files:** camelCase (`siteConfig.js`, `modules.js`)

### Vue Component Structure
Components follow this order: `name`, `components`, `props`, `data()`, `computed`, `methods`, `mounted`/lifecycle hooks. Use `<style lang="scss" scoped>` for component-specific styles.

### Component Patterns
- **Container components** (ResourceGrid, PostsGrid) handle data fetching and pagination
- **Presentational components** (GenericCard, PresentationalGrid) receive data via props
- **Global components** are registered in `plugins/Components.js` and available everywhere
- Communication: props down, events up, `$eventBus` for sibling components, Vuex for page state

### State Access
- Vuex state via `mapState`: `...mapState(['title', 'subtitle', 'content'])`
- Site config via injection: `this.$siteConfig.siteName`
- CMS resources: `this.$cms.post.getOne(slug)` / `this.$cms.category.getAll()`

## Configuration

Main site config is in `config/_siteConfig.js`. Supports environment variables for:
- Google Analytics ID (`GOOGLE_ANALYTICS_ID`)
- Site URL and other deployment settings

Key configurable options: site name, hero theme (mist/light/dark), layout (contained/full-width), sidebar position, card aspect ratios, Disqus shortname, Mailchimp settings, pagination counts.

## Build Pipeline

1. **before-build**: Runs `cms/netlify/hooks/beforeBuild.js` to generate static JSON API from markdown files into `static/api/`
2. **nuxt generate/build**: Nuxt compiles Vue components, generates routes from `config/generate.js` (maps `content/posts/*.md` and `content/categories/*.md` to URLs)
3. **after-build**: Runs `cms/netlify/hooks/afterBuild.js` for post-build cleanup
4. **Output**: Static HTML/JS/CSS in `dist/`

### Production Optimizations
- PurgeCSS removes unused CSS (whitelist in `config/build.js`)
- Responsive images generated at multiple sizes (640px to 1800px, 4 steps, JPEG quality 65%)
- Webpack bundle analysis enabled in production builds
- Nuxt code splitting (automatic)

## Testing

No test framework is configured. Code quality is maintained through ESLint linting only. Run `npm run lint` before committing.

## Key Files for Common Tasks

| Task | Files |
|------|-------|
| Add a new page | `pages/` (create .vue file, Nuxt auto-routes it) |
| Modify navigation | `config/_siteConfig.js` (menu array) |
| Change site theme/colors | `assets/scss/_vars.scss` + `config/_siteConfig.js` |
| Add a blog post | `content/posts/` (create .md file with frontmatter) |
| Add a category | `content/categories/` (create .md file with frontmatter) |
| Modify card display | `components/cards/` |
| Change layout structure | `layouts/default.vue` |
| Configure CMS fields | `cms/netlify/static/admin/config.yml` |
| Add a Nuxt module | `config/modules.js` |
| Add a Vue plugin | `plugins/` + `config/plugins.js` |
| Modify build behavior | `config/build.js` |
| Change head/meta tags | `config/head.js` |
