
### Native Python Installation (python.org or Homebrew)
- Install via `.pkg` file or `brew install python@3.13`
- Lives in `/usr/local/bin/` or `/opt/homebrew/bin/`
- **ONE system-wide version** (or messy manual PATH juggling)
- Breaking a project = potentially breaking your entire system

### Pyenv Installation
- **Multiple isolated versions** coexist peacefully 😌👌 (3.9, 3.11, 3.13, etc.)
- **Per-project version pinning** via `.python-version` file (no manual switching)
- **Automatic context switching** - cd into project, pyenv activates correct version
- **No sudo required** - installs in `~/.pyenv/`, zero system pollution
- **Clean uninstall** - nuke the `~/.pyenv/` directory, done
- **Build from source** - control compile flags, optimizations, dependencies

---

## The Actual Workflow Difference

**Without pyenv:**
```bash
cd legacy-project-needs-3.9
# Fuck. System has 3.13. Now what?
# Option A: Docker container (overkill?)
# Option B: Manually install 3.9, wrestle with PATH
# Option C: Give up, rewrite project for 3.13
```

**With pyenv:**
```bash
cd legacy-project-needs-3.9
pyenv local 3.9.18
# Done. Project uses 3.9.18. Other projects unaffected.

cd new-project-needs-3.13
pyenv local 3.13.0
# Done. This uses 3.13. Previous project still on 3.9.
```

---

## Is Pyenv Overkill?

**Use native Python if:**
- You only work on 1-2 projects
- All projects use the same Python version
- You don't mind reinstalling dependencies when upgrading Python

**Use pyenv if:**
- You juggle different Python requirements
- You test libraries across Python versions
- You want **reproducibility** (`.python-version` in git = team consistency)

---

## Installation Steps

### 1. Install pyenv via Homebrew
```bash
brew update
brew install pyenv
```

### 2. Fix potential GNU make issues (if installation hangs ⏳)
```bash
brew install make
echo 'export PATH="/opt/homebrew/opt/make/libexec/gnubin:$PATH"' >> ~/.zshrc
source ~/.zshrc
make --version  # Verify: should show GNU Make
which make      # Should output: /opt/homebrew/opt/make/libexec/gnubin/make
```

### 3. Install build dependencies for Python compilation
```bash
brew install openssl readline sqlite3 xz tcl-tk@8 libb2 zstd zlib pkg-config
```

### 4. Install latest Python 3.x + Verify
```bash
pyenv install 3  
```
```bash
pyenv versions
```

### 6. Set global Python version (system-wide default)
```bash
pyenv global 3.13.0  # Replace with your installed version
source ~/.zshrc
```

### 7. Set local Python version (project-specific)
```bash
cd /path/to/your/project
pyenv local 3.9.18 
source ~/.zshrc
```

---

## CheatSheet

| Command | What It Does |
|---------|-------------|
| `pyenv install --list` | Show all available Python versions |
| `pyenv install 3.13.0` | Install specific Python version |
| `pyenv versions` | List installed versions (* = active) |
| `pyenv global 3.13.0` | Set default version for all projects |
| `pyenv local 3.9.18` | Set version for current directory (creates `.python-version`) |
| `pyenv shell 3.11.0` | Set version for current shell session only (temporary) |
| `pyenv uninstall 3.9.18` | Remove a Python version |
| `pyenv which python` | Show path to active Python executable |

---

## Notes
- **Priority**: shell > local > global
---
- **Global**: affects all directories unless overridden
- **Local**: creates `.python-version` file in project directory (commit this to git!)
- **Shell**: temporary, current shell session only

---

## What Pyenv Does NOT Do

- Virtual environments (use `venv` or `virtualenv` on top of pyenv)
- Package management (still need `pip`, `poetry`, `pipenv`)
- IDE integration out of the box (you configure IDE to point at pyenv versions)

---

## Troubleshooting

**Python installation fails with build errors?**
```bash
# Make sure all dependencies are installed
brew upgrade openssl readline sqlite3 xz zlib

# Try with explicit flags
CFLAGS="-I$(brew --prefix openssl)/include" \
LDFLAGS="-L$(brew --prefix openssl)/lib" \
pyenv install 3.9.18
```

**Command not found: pyenv**
```bash
# Add to ~/.zshrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init --path)"' >> ~/.zshrc
source ~/.zshrc
```

**pyenv not switching versions?**
```bash
# Check your shell config
cat ~/.zshrc | grep pyenv

# Reinitialize
eval "$(pyenv init -)"
```