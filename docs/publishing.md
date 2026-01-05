# Publishing (PyPI)

SpecLite CLI uses GitHub Actions + PyPI Trusted Publishing (OIDC).

## One-time setup

1. Create the project on PyPI (and optionally TestPyPI).
2. In PyPI → **Publishing** → **Trusted Publishers**, add a publisher:
   - **Owner**: `speclite-dev`
   - **Repository**: `speclite`
   - **Workflow**: `.github/workflows/publish.yml`
   - **Environment**: (leave blank unless you want GitHub Environments)

## Publish

- **TestPyPI (manual):** Run the workflow “Publish to PyPI” with `repository=testpypi`.
- **PyPI (release):** Create a GitHub Release; the workflow publishes on `release.published`.

## Notes

- Publishing fails if the version in `pyproject.toml` already exists on the target index.
- The workflow builds both an sdist and a universal wheel.
