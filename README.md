# Project Silas

A text-based, persistent-world roleplaying game with no application. The entire game runs inside a coding-agent conversation: the wiki (`wiki/`) *is* the world state; `raw/` is where seeding sources and append-only session logs are kept — ground truth everything is compiled from; and [`CLAUDE.md`](CLAUDE.md) is the engine's own spec — the rituals that turn an LLM coding agent into both the Narrator and a silent World-Keeper.

If you've played tabletop Dungeons & Dragons, the feel of this will be familiar: the agent is the Dungeon Master — narrating the world, playing every NPC, adjudicating outcomes — while also keeping the campaign notes a human DM would keep between sessions. The one real difference is how much of the pen the player holds. By default here, you have considerably more narrative authority than a D&D player would at a real table: you can narrate not only your own actions, but also the outcome of your actions, NPC behavior, changes to the environment, or you could create a new character simply by mentioning them — anything, really, and the agent will run with it. If you'd rather play something more locked down, you can ask for whatever constraints you'd like during seeding, or between sessions (see caution, below).

**Caution:** changing any structural or load-bearing rules mid-campaign can be a bit risky. Close any open session (`/quit`) and make a `/save` point before doing so. Retrofitting a new rule can cause issues, but the agent is pretty good at ironing it all out. If you're unsure whether it's safe, you can always ask the agent to assess the impact and potentially come up with a custom implementation plan first.

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

**Running the engine on something other than Claude Code.** Nothing here is genuinely Claude-Code-specific except the filename `CLAUDE.md` and the assumption that whatever agent reads it has file read/write, shell, and git tools — all generic coding-agent capabilities. In principle, pointing an equivalent agent (Codex or similar) at this repo and having it treat CLAUDE.md as its operating instructions — likely by copying or adapting its content into whatever file that tool reads as its own system prompt (e.g. `AGENTS.md` for Codex) — should work the same way, since the actual mechanics are just file edits and git commits. This hasn't been tried. The main risk is an agent that follows the letter of the rituals more loosely: the "bookkeeping is invisible" and hidden/public boundary rules in particular depend on the agent actually holding to convention, since nothing here enforces them mechanically. After conversion, monitor the file writes at first, to make sure nothing lands in wiki/ without you seeing it narrated.

**Reading the wiki.** The wiki is a folder of Markdown files linked with standard relative Markdown links (`[Display Text](../folder/target.md)`), so pretty much anything that renders Markdown reads it correctly:

- **GitHub, once pushed** — renders each file and resolves the links, so you can click straight through the wiki from `index.md` the way you'd expect.
- **Obsidian** — open `wiki/` as a vault. Obsidian resolves standard Markdown links fine, so the graph view and backlinks still work even though the wiki isn't written in Obsidian's own `[[wikilink]]` shorthand.
- **A plain web browser or editor preview** — works too, as long as something is actually rendering Markdown to HTML (a raw `.md` file opened directly in a browser just shows text); most editors' built-in preview panes handle this natively.
