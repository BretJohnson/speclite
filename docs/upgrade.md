# Upgrade Guide

This guide covers updating the SpecLite CLI and refreshing your project's SpecLite files.

## Quick Reference

- **Use latest CLI (always):** `uvx speclite-cli --help`
- **Update project files:** `uvx speclite-cli install --ai <your-agent(s)>`

## Updating Project Files

When SpecLite releases new features (like new slash commands or updated templates), refresh your project's SpecLite files.

### What gets updated?

Running `uvx speclite-cli install` will update:

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
uvx speclite-cli install --ai <your-agent(s)>
```

Replace `<your-agent(s)>` with your AI assistant(s), comma-separated. Refer to this list of [Supported AI Agents](../README.md#-supported-ai-agents)

**Example:**

```bash
uvx speclite-cli install --ai copilot
```

`uvx speclite-cli install` is safe to re-run: it refreshes packaged defaults and preserves customized live templates.

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

If SpecLite detects that a default changed while you have customizations, it will back up the old default as `*.default.prev.md` and print instructions during `uvx speclite-cli install`.

---

## Common Scenarios

### Scenario 1: "I just want new slash commands"

```bash
uvx speclite-cli install --ai copilot
```

### Scenario 2: "I customized templates"

```bash
uvx speclite-cli install --ai copilot
```

### Working without Git

If your project isn't using Git branches, you'll need to manage feature directories manually:

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

### "Do I need to run this every time I open my project?"

**Short answer:** No, you only run `uvx speclite-cli install` once per project (or when upgrading).

**Explanation:**

The `speclite-cli` tool is used for:

- **Initial setup:** `uvx speclite-cli install` to bootstrap SpecLite in your project
- **Upgrades:** `uvx speclite-cli install` to update templates and commands
- **Diagnostics:** `uvx speclite-cli check` to verify tool installation

Once you've run `uvx speclite-cli install`, the slash commands (like `/sl.specify`, `/sl.plan`, etc.) are **permanently installed** in your project's agent folder (`.claude/`, `.github/prompts/`, etc.). Your AI assistant reads these command files directly—no need to run `speclite-cli` again.

**If your agent isn't recognizing slash commands:**

1. **Verify command files exist:**

   ```bash
   # For GitHub Copilot
   ls -la .github/prompts/

   # For Claude
   ls -la .claude/commands/
   ```

2. **Restart your IDE/editor completely** (not just reload window)

3. **Check you're in the correct directory** where you ran `uvx speclite-cli install`

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
- **Review release notes:** Check [GitHub Releases](https://github.com/speclite-dev/speclite/releases) for new features and breaking changes
- **Update workflows:** If new commands were added, update your team's development workflows
- **Check documentation:** Review the guides in [README](../README.md#-documentation) and the docs folder for updated instructions
