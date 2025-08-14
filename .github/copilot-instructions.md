# QuantEcon .github Repository

QuantEcon .github repository contains organization-level templates, environment configurations, and CI workflows for building QuantEcon's educational materials using Jupyter Book. This repository primarily supports building and testing lecture repositories across multiple platforms.

**ALWAYS follow these instructions first and only fallback to additional search and context gathering if the information in these instructions is incomplete or found to be in error.**

## Working Effectively

### Environment Setup
- Install conda/miniconda if not available: `conda --version`
- **Environment Creation**: `conda env create -f environment.yml` -- takes 15 minutes. NEVER CANCEL. Set timeout to 30+ minutes.
- **Note**: Environment creation may fail due to network timeouts during pip package installation. If this happens, the CI environment typically has packages pre-installed.

**Alternative: Use existing pre-installed environment (CI environments)**:
- **Activate environment**: `eval "$(conda shell.bash hook)" && conda activate quantecon`
- **Verify installation**: `jupyter-book --version` should show "Jupyter Book : 1.0.4.post1"

### Building Lecture Repositories
- **ALWAYS** activate the quantecon environment first: `eval "$(conda shell.bash hook)" && conda activate quantecon`
- **Clone lecture repository**: `git clone https://github.com/quantecon/lecture-python-programming.myst`
- **Build lectures**: `cd lecture-python-programming.myst && jb build lectures -W --keep-going` -- takes 4 minutes. NEVER CANCEL. Set timeout to 15+ minutes.
- **Alternative lecture repositories for testing**:
  - `https://github.com/quantecon/lecture-python.myst`
  - `https://github.com/quantecon/lecture-python-advanced.myst` 
  - `https://github.com/quantecon/python-lecture-sandpit.myst`

### Full CI Workflow Simulation
- **Test Linux workflow locally**: Run the commands from `.github/workflows/quantecon-env-linux.yml`
- **Complete test sequence** (each build takes 4+ minutes):
  ```bash
  eval "$(conda shell.bash hook)" && conda activate quantecon
  
  # Test lecture-python-programming
  git clone https://github.com/quantecon/lecture-python-programming.myst
  cd lecture-python-programming.myst
  jb build lectures -W --keep-going
  cd .. && rm -rf lecture-python-programming.myst
  
  # Test lecture-python  
  git clone https://github.com/quantecon/lecture-python.myst
  cd lecture-python.myst
  jb build lectures -W --keep-going
  cd .. && rm -rf lecture-python.myst
  
  # Test lecture-python-advanced
  git clone https://github.com/quantecon/lecture-python-advanced.myst
  cd lecture-python-advanced.myst
  jb build lectures -W --keep-going
  cd .. && rm -rf lecture-python-advanced.myst
  
  # Test python-lecture-sandpit
  git clone https://github.com/quantecon/python-lecture-sandpit.myst
  cd python-lecture-sandpit.myst
  jb build lectures
  cd .. && rm -rf python-lecture-sandpit.myst
  ```
- **Total time for full workflow**: 25+ minutes (15 min setup + 4 min × 4 builds). NEVER CANCEL. Set timeout to 45+ minutes.

## Validation

- **Always test environment setup** before making changes to environment.yml:
  1. Create test environment with your changes
  2. Activate environment and verify `jupyter-book --version` works
  3. Clone and build at least one lecture repository to verify full functionality
- **Build validation scenario**: After any changes, always test building `lecture-python-programming.myst` as it represents the standard workflow
- **Expected warnings**: Builds typically complete with warnings (exit code 1) - this is normal. Check for actual HTML output in `_build/html/` directory
- **Manual verification steps**:
  1. Verify HTML files are generated: `ls -la lectures/_build/html/*.html`
  2. Check for key files: `intro.html`, main lecture files, and `_static/` directory
  3. Verify build reports: `ls -la lectures/_build/html/reports/`

## Critical Timing Information

- **Environment Creation**: 15 minutes -- NEVER CANCEL. Allow 30+ minute timeout.
- **Single Lecture Build**: 4 minutes -- NEVER CANCEL. Allow 15+ minute timeout.
- **Full CI Workflow**: 25+ minutes total -- NEVER CANCEL. Allow 45+ minute timeout.
- **Large Package Downloads**: The anaconda=2025.06 package is very large (~280MB). Network speed affects timing.

## Known Issues and Workarounds

- **Network timeouts during environment creation**: The `conda env create` command may fail with pip timeout errors when downloading packages from PyPI. This is common in CI environments with network restrictions. Use the pre-installed environment when available.
- **Build warnings**: Jupyter Book builds typically show warnings and return exit code 1, but still produce valid output. Check `_build/html/` for actual results.
- **Network dependencies**: Some builds may fail to fetch external inventories (e.g., jax.quantecon.org) due to network restrictions - this is expected and doesn't prevent successful builds.
- **Conda base environment warnings**: conda often shows warnings about newer versions available - these can be ignored.

## Repository Structure

### Key Files
- `environment.yml`: Main conda environment specification
- `.github/workflows/quantecon-env-*.yml`: CI workflows for Linux, macOS, Windows testing
- `.github/dependabot.yml`: Dependabot configuration for GitHub Actions updates  
- `FUNDING.yml`: Sponsorship configuration
- `lectures/README.md`: Template for lecture repository README files

### GitHub Actions Workflows
- **quantecon-env-linux.yml**: Daily testing on Ubuntu (complete workflow)
- **quantecon-env-osx.yml**: Weekly testing on macOS (Monday)
- **quantecon-env-win.yml**: Weekly testing on Windows (Thursday)
- All workflows test building the same 4 lecture repositories

## Common Commands Reference

```bash
# Environment setup
conda env create -f environment.yml

# Environment setup (Alternative: Use existing environment in CI)
eval "$(conda shell.bash hook)" && conda activate quantecon

# Quick validation
jupyter-book --version
which jupyter-book

# Single repository test
git clone https://github.com/quantecon/lecture-python-programming.myst
cd lecture-python-programming.myst
jb build lectures -W --keep-going

# Check build results
ls -la lectures/_build/html/
ls -la lectures/_build/html/reports/

# Clean up
cd .. && rm -rf lecture-python-programming.myst
```

### Expected Command Output

#### conda env create -f environment.yml
```
# Takes ~15 minutes, downloads ~280MB of packages
# Shows package resolution, downloading, and installation progress
# Ends with: "done" and activation instructions
```

#### jupyter-book --version
```
Jupyter Book      : 1.0.4.post1
External ToC      : 1.0.1
MyST-Parser       : 3.0.1
MyST-NB           : 1.3.0
Sphinx Book Theme : 1.1.4
Jupyter-Cache     : 1.0.1
NbClient          : 0.10.2
```

#### jb build lectures -W --keep-going
```
# Takes ~4 minutes
# Shows Jupyter-Book and Sphinx build progress
# Typically ends with "build finished with problems, X warnings"
# Exit code 1 is normal - check _build/html/ for actual output
```