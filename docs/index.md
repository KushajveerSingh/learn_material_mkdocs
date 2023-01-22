
## Setup

### Install
Three options

- `pip install mkdocs-material`
- `docker pull squidfunk/mkdocs-material`
- `git clone https://github.com/squidfunk/mkdocs-material.git` and `cd mkdocs-material && pip install -e mkdocs-material`

### Upgrade
`pip install --upgrade --force-reinstall mkdocs-material`

### VSCode Setup
Create `.vscode/settings.json` file and add the following
```json
{
  "yaml.schemas": {
    "https://squidfunk.github.io/mkdocs-material/schema.json": "mkdocs.yml"
  },
  "yaml.customTags": [ 
    "!ENV scalar",
    "!ENV sequence",
    "tag:yaml.org,2002:python/name:materialx.emoji.to_svg",
    "tag:yaml.org,2002:python/name:materialx.emoji.twemoji",
    "tag:yaml.org,2002:python/name:pymdownx.superfences.fence_code_format"
  ]
}
```

### GitHub actions
To build and deploy the documentation automatically create a github action. Also, make sure `gh-pages` is selected for publishing the documentation in github.

Create `.github/workflows/build_docs.yml` and add the following
```yaml
name: build_docs 
on:
  push:
    branches:
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - uses: actions/cache@v2
        with:
          key: ${{ github.ref }}
          path: .cache
      - run: pip install mkdocs-material 
      - run: mkdocs gh-deploy --force
```

To install MkDocs plugins ([view_all](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Plugins)), add them in the second last line i.e.
```
pip install \
    mkdocs-material \
    mkdocs-awesome-pages-plugin \
    ...
```

## Basic commands

### Create docs
- `mkdocs new .`
- `docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material new .`

### Preview docs
The server will start at [localhost:8000](http://localhost:8000/)

- `mkdocs serve` or `mkdocs serve --dirtyreload` (if the project is large)
- `docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material`

### Build docs
Documentation would be available in `site` folder

- `mkdocs build`
- `docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material build`

## mkdocs.yml

### Font
```yaml
theme:
  # Direct integration with Google fonts.
  # font: false -> This would load system fonts
  font:
    text: Roboto # 300,400,400i, 700 would be used
    code: Roboto Mono # for code blocks and 400 would be used
```

### Logo and Favicon
```yaml
theme:
  # Provide logo in docs/assets/logo.png (can also be .svg)
  logo: assets/logo.png
  favicon: images/favicon.png
```

### Icons
Create `overrides/.icons/` directory and add all the svg icons you want to use. Then in `mkdocs.yml` add
```yaml
theme:
  custom_dir: overrides

markdown_extensions:
  - pymdownx.emoji:
      emoji_index: !!python/name:materialx.emoji.twemoji
      emoji_generator: !!python/name:materialx.emoji.to_svg
      options:
        custom_icons:
          - overrides/.icons
```

Then you can use the icons in markdown files as `:folder-icon-name:` (replace all `/` with `-`).

### Complying with GDPR
```yaml
extra:
  consent:
    title: Cookie consent
    description: >-
      We use cookies to recognize your repeated visits and preferences, as well
      as to measure the effectiveness of our documentation and whether users
      find what they're searching for. With your consent, you're helping us to
      make our documentation better.

# As per GDPR people should be able to change the cookies at any time.
copyright: >
  Copyright &copy; 2016 - 2023 Kushajveer Singh -
  <a href="#__consent">Change cookie settings</a>

# Download any external assets used by your website (like fonts). Useful for
# self hosting and for GDPR. It is faster to load fonts from Google instead of
# self hosting.
plugins:
  - privacy
```

### Navigation
```yaml
theme:
  features:
    # Internal links will not result in full-reloading of the page
    - navigation.instant

    # URL is automatically updated with the active anchor as highlighted 
    # in table of contents
    - navigation.tracking

    # Top-level sections are rendered in a menu layer below the header (disabling it seems better)
    - navigation.tabs

    # Top-level sections are rendered as groups in the sidebar
    # (disabling it seems better)
    - navigation.sections

    # Add breadcrumb at top of each page, to make navigation easier
    # on mobile
    - navigation.path

    # For sites with a 100+ pages, the navigation links in the sidebar
    # can take a lot of space. This feature can be used to reduce the
    # size of the rendered HTML by more than 33% by not rendering the
    # things that are not visible.
    - navigation.prune

    # In the left-sidebar, you can click the section heading and show
    # an overview page. Create `index.md` file in the respective folder
    # and add it to the beginning of the navigation section (as shown
    # below in 'nav').
    - navigation.indexes

    # Sidebar is automatically scrolled so that the active anchor is always
    # visible.
    - toc.follow

    # Show back-to-top button when user starts scrolling up
    - navigation.top
nav:
  - Section:
    - section/index.md
    - Page 1: section/page-1.md
    ...
    - Page n: section/page-n.md
```

To hide things, add this to the frontmatter of markdown file
```md
---
hide:
  - navigation <-- the left sidebar
  - toc <-- the right sidebar>
  - path <-- breadcrumb if you have enabled it
---

# Document title
```

## Keyboard shortcuts
When search is focused
- Down / Up - select next/previous result
- Esc / Tab - close search dialog
- Enter - follow selected research

WHen search is not focused
- F / S / / - open search dialog
- P / , - goto previous page
- N / . - goto next page

## Change content width
In `mkdocs.yml`
```yaml
extra_css:
  - stylesheets/extra.css
```

In `docs/stylesheets/extra.css`
```css
.md-grid {
    // set to 'initial' to use the entire available space
    max-width: 1440px;
}
```

## Search
```yaml
# Works offline also
plugins:
  - search

theme:
  features:
    # Show suggestions for the current word being typed
    - search.suggest

    # When user clicks a search result, all occurrences are highlighted
    - search.highlight
```

Markdown frontmatter
```md
---
search:
  boost: 2 (to rank page higher)
  boost: 0.2 (to rank page lower)

  exclude: true (exclude page from search)
---

## Section 2 { data-search-exclude } <-- to exclude a section from search

Some content block { data-search-exclude } <-- can also be used to exclude inline and block level elements
```

## Google analytics
```yaml
extra:
  analytics:
    provider: google
    property: G-XXXXXXXX
```

## Social preview images
Images that are displayed when a link to your project documentation is shared on social media.

1. `apt-get install libcairo2-dev libfreetype6-dev libffi-dev libjpeg-dev libpng-dev libz-dev`
2. `pip install pillow cairosvg`
3. Add (this will add preview images to `assets/images/social`)
    ```yaml
    plugins:
      - social
    ```
4. Add `.cache` directory to `.gitignore` when building site locally. Then in github action you can build the cache.

## Blog
```yaml
plugins:
  - blog

nav:
  - Blog:
    - blog/index.md <-- to show archive and category
```

Archive and category would be automatically generated.
- Archive - you can see the posts for the past years (year by year)
- Category - see posts by category

The folder structure is
```
.
├─ docs/
│  └─ blog/
│     ├─ posts/
│     └─ index.md
└─ mkdocs.yml
```
