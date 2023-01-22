
## Setup

### Install
Three options

- `pip install mkdocs-material`
- `docker pull squidfunk/mkdocs-material`
- `git clone https://github.com/squidfunk/mkdocs-material.git` and `cd mkdocs-material && pip install -e mkdocs-material`

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

