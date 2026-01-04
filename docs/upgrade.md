# Upgrade Guide

> You have SpecLite installed and want to upgrade to the latest version to get new features, bug fixes, or updated slash commands. This guide covers both upgrading the CLI tool and updating your project files.

---

## Quick Reference

| What to Upgrade | Command | When to Use |
|----------------|---------|-------------|
| **CLI Tool Only** | `uv tool install speclite-cli --force --from git+https://github.com/BretJohnson/speclite.git` | Get latest CLI features without touching project files |
| **Project Files** | `speclite init --here --force --ai <your-agent(s)>` | Update slash commands, templates, and scripts in your project |
| **Both** | Run CLI upgrade, then project update | Recommended for major version updates |

---

## Part 1: Upgrade the CLI Tool

The CLI tool (`speclite`) is separate from your project files. Upgrade it to get the latest features and bug fixes.

### If you installed with `uv tool install`

```bash
uv tool install speclite-cli --force --from git+https://github.com/BretJohnson/speclite.git
```

### If you use one-shot `uvx` commands

No upgrade needed—`uvx` always fetches the latest version. Just run your commands as normal:

```bash
uvx --from git+https://github.com/BretJohnson/speclite.git speclite init --here --ai copilot
```

### Verify the upgrade

```bash
speclite check
```

This shows installed tools and confirms the CLI is working.

---

## Part 2: Updating Project Files

When SpecLite releases new features (like new slash commands or updated templates), you need to refresh your project's SpecLite files.

### What gets updated?

Running `speclite init --here --force` will update:

- ✅ **Slash command files** (`.claude/commands/`, `.github/prompts/`, etc.)
- ✅ **Script files** (`.speclite/scripts/`)
- ✅ **Template defaults** (`.speclite/templates/*.default.md`) *(always refreshed)*
- ✅ **Template live files** (`.speclite/templates/*.md`) *(updated only if uncustomized; customized files are preserved)*

### What stays safe?

These files are **never touched** by the upgrade—the template packages don't even contain them:

- ✅ **Your change specifications** (`.speclite/changes/001-my-feature/spec.md`, etc.) - **CONFIRMED SAFE**
- ✅ **Your implementation plans** (`.speclite/changes/001-my-feature/plan.md`, `tasks.md`, etc.) - **CONFIRMED SAFE**
- ✅ **Shared memory files** (`.speclite/memory/`) - **CONFIRMED SAFE**
- ✅ **Your source code** - **CONFIRMED SAFE**
- ✅ **Your git history** - **CONFIRMED SAFE**

### Update command

Run this inside your project directory:

```bash
speclite init --here --force --ai <your-agent(s)>
```

Replace `<your-agent(s)>` with your AI assistant(s), comma-separated. Refer to this list of [Supported AI Agents](../README.md#-supported-ai-agents)

**Example:**

```bash
speclite init --here --force --ai copilot
```

### Understanding the `--force` flag

Older versions prompted for confirmation when running `speclite init --here` in a non-empty directory. Current versions proceed without prompting; `--force` is kept for compatibility.

---

## ⚠️ Important Notes

### 1. Constitution file

Your existing `memory/constitution.md` file will be untouched. If you wish
to recreate the constiution with the possibly updated constitution template,
delete the constitution and then rerun `/sl.constitution`.


```bash
rm .speclite/memory/constitution.md
# Then run /sl.constitution in your agent to recreate it
```

### 2. Custom template modifications

If you customized any templates in `.speclite/templates/`, the upgrade preserves your live copies (`*.md`). The packaged defaults are stored alongside as `*.default.md` and are refreshed during upgrade.

If SpecLite detects that a default changed while you have customizations, it will back up the old default as `*.default.prev.md` and print instructions during `speclite init`.

---

## Common Scenarios

### Scenario 1: "I just want new slash commands"

```bash
# Upgrade CLI (if using persistent install)
uv tool install speclite-cli --force --from git+https://github.com/BretJohnson/speclite.git

# Update project files to get new commands
speclite init --here --force --ai copilot
```

Your constitution is preserved automatically.

### Scenario 2: "I customized templates"

```bash
# 1. Upgrade CLI
uv tool install speclite-cli --force --from git+https://github.com/BretJohnson/speclite.git

# 2. Update project
speclite init --here --force --ai copilot
```

### Scenario 3: "I'm working on a project without Git"

If you initialized your project with `--no-git`, you can still upgrade:

```bash
# Run upgrade
speclite init --here --force --ai copilot --no-git
```

The `--no-git` flag skips git initialization but doesn't affect file updates.

---

## Using `--no-git` Flag

The `--no-git` flag tells SpecLite to **skip git repository initialization**. This is useful when:

- You manage version control differently (Mercurial, SVN, etc.)
- Your project is part of a larger monorepo with existing git setup
- You're experimenting and don't want version control yet

**During initial setup:**

```bash
speclite init my-project --ai copilot --no-git
```

**During upgrade:**

```bash
speclite init --here --force --ai copilot --no-git
```

### What `--no-git` does NOT do

❌ Does NOT prevent file updates
❌ Does NOT skip slash command installation
❌ Does NOT affect template merging

It **only** skips running `git init` and creating the initial commit.

### Working without Git

If you use `--no-git`, you'll need to manage feature directories manually:

**Set the `SPECLITE_FEATURE` environment variable** before using planning commands:

```bash
# Bash/Zsh
export SPECLITE_FEATURE="001-my-feature"

# PowerShell
$env:SPECLITE_FEATURE = "001-my-feature"
```

This tells SpecLite which change spec directory to use when creating plans and tasks.

**Why this matters:** Without git, SpecLite can't detect your current branch name to determine the active feature. The environment variable provides that context manually.

---

## Troubleshooting

### "Slash commands not showing up after upgrade"

**Cause:** Agent didn't reload the command files.

**Fix:**

1. **Restart your IDE/editor** completely (not just reload window)
2. **For CLI-based agents**, verify files exist:

   ```bash
   ls -la .claude/commands/      # Claude Code
   ls -la .gemini/commands/       # Gemini
   ls -la .cursor/commands/       # Cursor
   ```

3. **Check agent-specific setup:**
   - Codex requires `CODEX_HOME` environment variable
   - Some agents need workspace restart or cache clearing

**What gets overwritten:**

Only SpecLite infrastructure files:

- Agent command files (`.claude/commands/`, `.github/prompts/`, etc.)
- Scripts in `.speclite/scripts/`
- Templates in `.speclite/templates/` (defaults refreshed; customized live files preserved)

**What stays untouched:**

- Your `.speclite/changes/` directory (specifications, plans, tasks)
- Your source code files
- Your `.git/` directory and git history
- Any other files not part of SpecLite templates
- Memory files in `.speclite/memory/` (constitution is created via `/sl.constitution`)

Verify the installation:

```bash
# Check installed tools
uv tool list

# Should show speclite-cli

# Verify path
which speclite

# Should point to the uv tool installation directory
```

If not found, reinstall:

```bash
uv tool uninstall speclite-cli
uv tool install speclite-cli --from git+https://github.com/BretJohnson/speclite.git
```

### "Do I need to run speclite every time I open my project?"

**Short answer:** No, you only run `speclite init` once per project (or when upgrading).

**Explanation:**

The `speclite` CLI tool is used for:

- **Initial setup:** `speclite init` to bootstrap SpecLite in your project
- **Upgrades:** `speclite init --here --force` to update templates and commands
- **Diagnostics:** `speclite check` to verify tool installation

Once you've run `speclite init`, the slash commands (like `/sl.specify`, `/sl.plan`, etc.) are **permanently installed** in your project's agent folder (`.claude/`, `.github/prompts/`, etc.). Your AI assistant reads these command files directly—no need to run `speclite` again.

**If your agent isn't recognizing slash commands:**

1. **Verify command files exist:**

   ```bash
   # For GitHub Copilot
   ls -la .github/prompts/

   # For Claude
   ls -la .claude/commands/
   ```

2. **Restart your IDE/editor completely** (not just reload window)

3. **Check you're in the correct directory** where you ran `speclite init`

4. **For some agents**, you may need to reload the workspace or clear cache

**Related issue:** If Copilot can't open local files or uses PowerShell commands unexpectedly, this is typically an IDE context issue, not related to `speclite`. Try:

- Restarting VS Code
- Checking file permissions
- Ensuring the workspace folder is properly opened

---

## Version Compatibility

SpecLite follows semantic versioning for major releases. The CLI and project files are designed to be compatible within the same major version.

**Best practice:** Keep both CLI and project files in sync by upgrading both together during major version changes.

---

## Next Steps

After upgrading:

- **Test new slash commands:** Run `/sl.constitution` or another command to verify everything works
- **Review release notes:** Check [GitHub Releases](https://github.com/BretJohnson/speclite/releases) for new features and breaking changes
- **Update workflows:** If new commands were added, update your team's development workflows
- **Check documentation:** Review the guides in [README](../README.md#-documentation) and the docs folder for updated instructions
