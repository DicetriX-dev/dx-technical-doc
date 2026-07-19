# DX Technical Documentation

This repository contains technical documentation for DicetriX products, including API documentation, user manuals, versioned PDF files, and a searchable documentation website.

Documentation is written in Markdown, document metadata and versions are maintained in YAML, PDFs are generated using Pandoc and LaTeX, and the website is built with Material for MkDocs and deployed through GitHub Pages.

## Products

- **DX CRM**
- **DX Travelz**

## Repository Structure

```text
DX-TECHNICAL-DOC/
├── .github/
│   └── workflows/
│       └── build-and-deploy.yml
├── documents/
│   ├── index.md
│   ├── versions.md
│   ├── assets/
│   │   ├── images/
│   │   ├── javascripts/
│   │   └── stylesheets/
│   │       └── extra.css
│   ├── dx-crm/
│   │   ├── index.md
│   │   ├── api.md
│   │   └── usermanual.md
│   └── dx-travelz/
│       ├── index.md
│       ├── api.md
│       └── usermanual.md
├── scripts/
│   ├── build.sh
│   └── generate_version_page.py
├── templates/
│   └── metadata.yml
├── dist/
├── site/
├── documents.yml
├── mkdocs.yml
├── requirements.txt
├── Makefile
└── README.md
```

## Documentation Website

The website is generated with Material for MkDocs and provides:

- product-specific documentation;
- API references and user manuals;
- full-text search;
- light and dark modes;
- document-version information;
- downloadable PDF files.

### Run Locally

Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Generate the document-version page:

```bash
python scripts/generate_version_page.py
```

Start the development server:

```bash
mkdocs serve
```

Open:

```text
http://127.0.0.1:8000
```

### Build the Website

```bash
mkdocs build --clean --strict
```

The generated website is written to `site/`. This directory is generated automatically and should not normally be committed.

## Document Configuration

Document-specific metadata is maintained in `documents.yml`.

```yaml
products:
  dx-crm:
    name: "DicetriX CRM"

    documents:
      api:
        title: "DicetriX CRM API Documentation"
        source: "documents/dx-crm/api.md"
        version: "1.0.0"
        status: "approved"
        effective_date: "2026-07-19"
        owner: "DicetriX"
        output_name: "dx-crm-api"
```

| Field | Description |
|---|---|
| `title` | Document title |
| `source` | Markdown source path |
| `version` | Published document version |
| `status` | Document lifecycle status |
| `effective_date` | Effective date of the current version |
| `owner` | Document owner |
| `output_name` | Base name of the generated PDF |

Shared Pandoc and LaTeX configuration is maintained in `templates/metadata.yml`.

## Document Versioning

Documents use semantic versioning:

```text
MAJOR.MINOR.PATCH
```

Examples:

```text
1.0.0  Initial release
1.1.0  New sections or meaningful content updates
1.1.1  Minor corrections or formatting changes
2.0.0  Major restructuring or incompatible changes
```

To publish a new version, update the relevant entry in `documents.yml`:

```yaml
version: "1.1.0"
effective_date: "2026-08-01"
```

Commit the version change together with the related Markdown changes.

## Building PDF Documents

The PDF build requires Pandoc, XeLaTeX, `yq`, and GNU Make.

### Build All Documents

```bash
make build
```

Alternatively:

```bash
./scripts/build.sh all
```

### Build a Specific Document

```bash
./scripts/build.sh document dx-crm api
```

Available Makefile targets may include:

```bash
make crm-api
make crm-manual
make travelz-api
make travelz-manual
```

### Generated PDFs

```text
dist/
└── dx-crm/
    ├── dx-crm-api-latest.pdf
    └── v1.1.0/
        └── dx-crm-api-v1.1.0.pdf
```

Each build produces a version-specific PDF and a stable `latest` PDF. The `dist/` directory is generated automatically and does not need to be committed when GitHub Actions handles publication.

## GitHub Actions

### `build-documents.yml`

This workflow is intended for document validation, particularly on pull requests. It builds the PDFs, verifies that the build succeeds, and uploads the generated files as workflow artifacts without deploying the website.

### `build-and-deploy.yml`

This workflow is intended for production builds after changes are pushed or merged into `main`. It builds the PDFs, copies them into the website, generates the version page, builds the MkDocs website, uploads the PDF artifact, and deploys the website to GitHub Pages.

Recommended workflow arrangement:

```text
Pull request
    → build-documents.yml
    → validate PDFs

Push or merge to main
    → build-and-deploy.yml
    → build PDFs
    → build website
    → deploy to GitHub Pages
```

## GitHub Pages

Configure GitHub Pages using:

```text
Settings → Pages → Build and deployment → Source → GitHub Actions
```

The deployment workflow publishes the generated `site/` directory automatically.

## Source Assets

Commit website source assets such as:

```text
documents/assets/stylesheets/extra.css
documents/assets/images/
documents/assets/javascripts/
documents/index.md
mkdocs.yml
```

Do not normally commit generated output:

```text
site/
dist/
```

Recommended `.gitignore`:

```gitignore
# Generated documentation
site/
dist/

# Python
.venv/
__pycache__/
*.pyc

# macOS
.DS_Store
```

## Adding a New Document

Create the Markdown source:

```text
documents/dx-crm/installation-guide.md
```

Add its configuration to `documents.yml`:

```yaml
installation-guide:
  title: "DicetriX CRM Installation Guide"
  source: "documents/dx-crm/installation-guide.md"
  version: "1.0.0"
  status: "draft"
  effective_date: "2026-07-19"
  owner: "DicetriX"
  output_name: "dx-crm-installation-guide"
```

Add the document to the `nav` section in `mkdocs.yml` when it should appear on the website, then build and verify:

```bash
make build
python scripts/generate_version_page.py
mkdocs build --clean --strict
```

## Adding a New Product

Create a product directory such as `documents/dx-new-product/`, add the product and its documents to `documents.yml`, and add its pages to the `nav` section in `mkdocs.yml`.

## Recommended Release Workflow

1. Update the Markdown source.
2. Update the version and effective date in `documents.yml`.
3. Build and review the PDF locally.
4. Build and review the website locally.
5. Commit the source, configuration, and assets.
6. Push the changes or open a pull request.
7. Confirm that GitHub Actions completes successfully.
8. Optionally create a Git tag.

Example:

```bash
git add documents/dx-crm/api.md documents.yml
git commit -m "Release DX CRM API documentation v1.1.0"
git push origin main
```

Optional tag:

```bash
git tag docs/dx-crm-api/v1.1.0
git push origin docs/dx-crm-api/v1.1.0
```

## Document Status Values

| Status | Meaning |
|---|---|
| `draft` | Document is under development |
| `review` | Document is awaiting review |
| `approved` | Document is approved for use |
| `deprecated` | Document has been superseded |
| `archived` | Document is retained for historical reference |

## Contributing

When updating documentation:

- use clear and consistent technical language;
- keep headings and formatting consistent;
- update the document version when published content changes;
- update the effective date for approved releases;
- build and review both PDF and website outputs;
- avoid editing generated files directly;
- use descriptive commit messages.

## License

This repository and its documentation are proprietary to DicetriX unless otherwise stated.

Unauthorized copying, distribution, modification, or publication is prohibited.

