# bulletproof-react-skills

A Claude Code plugin: 7 React project-convention skills distilled from
[alan2207/bulletproof-react](https://github.com/alan2207/bulletproof-react)'s `docs/*.md`
files (MIT licensed) — project structure, state and data fetching, testing, performance,
security, components and styling, and project standards.

Each skill is produced by a scripted, re-runnable generator (`generate.js`) that reads a
pinned bulletproof-react commit, plus an LLM-assisted polish pass (`polish.js`) that
rewrites the mechanical draft into agent-directed guidance — rather than a hand-vendored
copy that goes stale silently. See [`NOTICE.md`](./NOTICE.md) for full provenance, the
license text, and the re-pin/update workflow.

## Why a standalone repo

This plugin was previously a sibling directory inside
[dfadler/agent-config](https://github.com/dfadler/agent-config), a cross-project agent
config repo. React-specific content riding along in a plugin every project on that
machine loads didn't fit that repo's scope, so it was extracted here — a plain
lift/shift, same content, same generator, same provenance — and is now distributed the
same way [mattpocock/skills](https://github.com/mattpocock/skills) is: a standalone
marketplace-style repo you add explicitly, rather than something bundled into another
tool's config. See [dfadler/agent-config#226](https://github.com/dfadler/agent-config/issues/226).

## Install

This repo is not in Claude Code's official plugin marketplace, so add it as a
marketplace first, then install the plugin from it:

```bash
claude plugin marketplace add dfadler/bulletproof-react-skills
claude plugin install bulletproof-react-skills
```

Or, from inside a Claude Code session:

```
/plugin marketplace add dfadler/bulletproof-react-skills
/plugin install bulletproof-react-skills
```

Once installed, the skills are available under the `bulletproof-react-skills:` namespace
(e.g. `bulletproof-react-skills:project-structure`) and trigger automatically when a
task matches — Claude Code decides based on each skill's `description` frontmatter, not
a command you run yourself.

## Skills

| Skill | Covers |
| --- | --- |
| `project-structure` | What lives in `src/`, the app/assets/components/features/hooks/lib/stores/testing/types/utils layout, unidirectional feature organization. |
| `state-and-data` | Component vs. application vs. server vs. form vs. URL state, a shared API client instance, colocated request declarations with React Query, API/in-app error handling. |
| `testing` | Unit/integration/e2e/static/visual-regression test types, mocking with MSW, what to prioritize. |
| `performance` | Code splitting, render/state update optimization, list virtualization, image optimization, bundle analysis. |
| `security` | Token storage and authentication for SPAs, authorization patterns, client-side hardening. |
| `components-and-styling` | Colocation, avoiding nested render functions, container/presentation separation, composition over config. |
| `project-standards` | ESLint/Prettier/TypeScript config, absolute imports, pre-commit hooks, CI conventions. |

## Updating

This is a manual process today — there is no scheduled or CI job that checks for a new
upstream bulletproof-react commit and opens a re-pin PR automatically.

1. **Find the new commit to pin:**

   ```bash
   git ls-remote https://github.com/alan2207/bulletproof-react.git HEAD
   ```

2. **Bump the pin** in `package.json`'s `devDependencies`, then reinstall:

   ```
   "bulletproof-react": "git+https://github.com/alan2207/bulletproof-react.git#<new-sha>"
   ```

   ```bash
   npm install
   ```

   This respects the local `.npmrc` (`ignore-scripts=true`): bulletproof-react's own
   `package.json` has a `prepare` script that would otherwise try to install three
   example apps' worth of dependencies just to fetch `docs/*.md`.

3. **Re-run the generator** (`npm run generate`) and read its output. It refuses to
   proceed — rather than silently regenerating around the gap — if the doc set no
   longer matches its mapping (a file added, removed, or renamed); in that case, update
   `generate.js`'s `SKILL_MAP`/`DROPPED` first.

4. **Review the manifest diff** (`.docs-manifest.json`, per-file content hashes) to see
   which source docs actually changed since the last generation.

5. **Run the polish step** (`npm run polish`) to rewrite each changed skill from
   generate.js's mechanical draft into agent-directed guidance, via `claude -p`. Skips
   any skill whose source hasn't changed since it was last polished (tracked in
   `.polish-manifest.json`), so this only spends an API call — and only re-rolls wording
   — on skills the pin bump actually touched.

6. **Review each changed `SKILL.md`** against its source doc(s) anyway — an LLM rewrite
   can still get a technical detail subtly wrong, especially on security-sensitive
   content.

7. **Commit** the updated `SKILL.md` files, `.docs-manifest.json`,
   `.polish-manifest.json`, the new pinned SHA/date in `NOTICE.md`, and a bumped
   `version` in `.claude-plugin/plugin.json`.

## License

The generator scripts and plugin manifest here are provided as-is by
[dfadler](https://github.com/dfadler). The skill content itself is adapted from
bulletproof-react, MIT licensed by Alan Alickovic — see [`NOTICE.md`](./NOTICE.md) for
the full license text and provenance.
