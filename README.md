# Project Silas

A text-based, persistent-world roleplaying game with no application. The entire game runs inside a coding-agent conversation: the wiki (`wiki/`) *is* the world state, `raw/` is the append-only ground truth everything is compiled from, and [`CLAUDE.md`](CLAUDE.md) is the engine's own spec — the rituals that turn an LLM coding agent into both the Narrator and a silent World-Keeper.

If you've played tabletop Dungeons & Dragons, the shape of this will be familiar: the agent is the whole table by itself, acting as Dungeon Master — narrating the world, playing every NPC, adjudicating outcomes — while also keeping the campaign notes a human DM would keep between sessions. The one real difference is how much of the pen the player holds. By default here, you have considerably more narrative authority than a D&D player would at a real table: you can narrate outcomes and specifics in your own turns, and — unless the world was seeded to say otherwise — you can describe what NPCs say and do yourself, or introduce a new named character just by mentioning them, and the agent will generally run with it. If you'd rather play something closer to a traditional table, you can ask for that structure during seeding — for example, that only the agent may introduce new characters, or that the player may narrate their own character only, never an NPC's words or actions. Both of those constraints, and others like them, are yours to request; neither is the default.

**A caution that follows from this:** the constraints above are structural — load-bearing for how the whole story gets told from then on. They're easy to set at seeding, before any history depends on them, and much riskier to change once a campaign is already running: retrofitting a new rule onto continuity that was written under the old one can contradict things already on the record. If you want to change something structural mid-story, expect to do it deliberately and outside a session (see CLAUDE.md's note that `rules.md` changes only from outside play), and to have the agent check the existing wiki for conflicts rather than just flipping the rule.

## Start here

**Read [`CLAUDE.md`](CLAUDE.md) in full before doing anything else.** It's the actual documentation — repository layout, session rituals (opening, playing, closing), the hidden/public boundary, page conventions, time rules, and the git/branch model that makes each world its own branch. This README doesn't duplicate any of that; it's orientation for whatever CLAUDE.md doesn't cover, which turns out to be not much.

## Prerequisites

- Git, and a clone of this repo.
- [Claude Code](https://claude.com/claude-code) (CLI, desktop app, or an equivalent agent — see "Alternatives," below) with access to a Claude model, able to read/write files, run a shell, and use git.
- **Permissions matter here more than in a typical coding project.** The agent needs full automatic, silent read *and* write access to the `wiki/` folder (and `raw/`, where it logs every session) — no per-file confirmation prompts. CLAUDE.md's rituals depend on this explicitly: bookkeeping happens invisibly, several files at a time, every single turn, and a permission prompt on each one breaks both the immersion and the rule that only the Narrator's prose is ever shown. Configure your agent to auto-approve file edits (and ideally git commands) within this repo before you start.
- Nothing else beyond that. No build step, no dependencies, no server. The "engine" is CLAUDE.md's instructions plus an agent willing to follow them.

## Editing the wiki directly

Every file under `wiki/` is plain Markdown, so hand-editing one will "work" in the sense that the file changes. It's strongly discouraged anyway: a direct edit skips the logging (nothing lands in `raw/`, nothing appends to `log.md`) and skips the dependency updates the agent does automatically — index lines, cross-links, neighboring pages that reference the thing you just changed, the hidden/public boundary. Ask the agent to make the change instead, even outside a session in a workshop conversation. It's the same edit, just one that keeps the rest of the wiki honest.

## Starting a new world

Condensed from CLAUDE.md's "Seeding a world from old chats":

1. Branch from `main`: `git checkout -b <world-name> main`. `main` itself is the empty engine — no world lives there.
2. Give the agent something to seed from — old chat exports dropped in `raw/sources/`, ingested in chronological order — or, if you have nothing to import, just describe the setting in conversation and have it build a world from scratch.
3. This is a workshop conversation, not play: the agent builds characters, places, and history into `wiki/` as you go, hidden pages included, and sets `wiki/world/clock.md` once seeding is done.
4. Commit, then say "open session" (or `/open`) to take your first turn.

## The worlds that already exist here

- **[`aldenmere`](https://github.com/Christopher-M-Dunn/project_silas_2/tree/aldenmere)** — a fantasy world of shapeshifting, bound magic, and a missing brother, centered on the town of Briarhollow; imported from a different system's chat history.
- **[`ktarluud`](https://github.com/Christopher-M-Dunn/project_silas_2/tree/ktarluud)** — a shipwrecked survey crew's survival and first-contact story on an unnamed ocean world; created natively here from a seeding prompt, no import.

To look at either: `git checkout aldenmere` or `git checkout ktarluud`, then open `wiki/index.md` to see what a populated world looks like.

## Tech stack

There isn't one, and that's the point. The whole "application" is:

- **Markdown files** (`wiki/`, `raw/`) serving as both the world's database and its UI.
- **Git** for save points, branching, timelines, and rollback — see CLAUDE.md's "Git — saves, timelines, worlds."
- **An LLM coding agent** that treats `CLAUDE.md` as its spec and plays the Narrator and World-Keeper roles turn by turn.

No frontend, no backend, no database beyond git history, no package manager, nothing to install.

## Alternatives and hypothetical conversions

**Running the engine on something other than Claude Code.** Nothing here is genuinely Claude-Code-specific except the filename `CLAUDE.md` and the assumption that whatever agent reads it has file read/write, shell, and git tools — all generic coding-agent capabilities. In principle, pointing an equivalent agent (Codex or similar) at this repo and having it treat CLAUDE.md as its operating instructions — likely by copying or adapting its content into whatever file that tool reads as its own system prompt (e.g. `AGENTS.md` for Codex) — should work the same way, since the actual mechanics are just file edits and git commits. This hasn't been tried. The main risk is an agent that follows the letter of the rituals more loosely: the "bookkeeping is invisible" and hidden/public boundary rules in particular depend on the agent actually holding to convention, since nothing here enforces them mechanically.

**Reading the wiki without an agent.** The wiki is a folder of Markdown files linked with `[[wikilink]]`-style cross-references, so a few things can read it:

- **Obsidian** — open `wiki/` as a vault. Obsidian understands `[[wikilinks]]` natively, so the graph view and backlinks work immediately; this is the closest thing to a "real" viewer the format has.
- **A plain web browser or editor preview** — renders individual Markdown files fine, but `[[wikilinks]]` won't become clickable links without something Obsidian-like resolving them.
- **GitHub, once pushed** — renders each file's Markdown formatting on its own, but GitHub doesn't resolve `[[wikilink]]` syntax either (it expects full relative paths), so browsing is more "read `index.md`, click into files by hand" than true wiki navigation.
