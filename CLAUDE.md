# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **learntla-v2**, a Sphinx-based documentation site for learning TLA+ (Temporal Logic of Actions) specification language. The site is authored by Hillel Wayne and deployed to AWS S3.

## Build Commands

```bash
# Install dependencies (requires Python 3.10+, graphviz)
uv pip install -r requirements.txt

# Build with TODOs visible (development)
sphinx-build docs/ docs/_build/html/

# Build without TODOs (production)
sphinx-build -D todo_include_todos=0 docs/ docs/_build/html/

# Check for broken links
sphinx-build -b linkcheck docs/ docs/_build/
```

## Working with TLA+ Specs

Raw specs live in `raw-specs/` with YAML headers containing metadata (target path, state space info). The `process_spec.py` script converts them to publishable form:

```bash
# Process a spec (from raw-specs/ directory)
python process_spec.py file.tla

# Expand XML templates (generates multiple spec variants)
python expand_template.py --dryrun file.xml  # preview
python expand_template.py file.xml           # write files
```

Processing strips PlusCal translations, renames MODULE declarations, and updates `docs/data.yml` with state space metadata.

## Architecture

- **docs/** - Sphinx documentation source
  - `conf.py` - Sphinx config (uses piccolo_theme, custom TLA+ syntax highlighting via tla-pygments)
  - `_extensions/` - Custom directives: `spec` (embeds specs with download links), `state_space`, `exercise`, `troubleshooting`
  - `specs/` - Published TLA+ spec files (generated from raw-specs)
  - `data.yml` - State space metadata referenced by `state_space` directive
  - Content organized: `core/` (tutorial), `topics/` (advanced), `examples/`, `reference/`

- **raw-specs/** - Source specs with YAML headers
  - `.xml` files are templates for generating multiple spec variants
  - Processed specs go to `docs/specs/`

## Custom Sphinx Directives

- `.. spec:: filename.tla` - Embeds spec from docs/specs/ with download link, optional `:ss:` for state space, `:fails:` flag
- `.. state_space:: name` - Renders state space info from data.yml
- `.. exercise::` - Wraps exercise content
- `.. troubleshooting::` - Troubleshooting sections
