# Decision Drill

**English** · [Русский](README.ru.md)

Author: **AronHopeless**

A Claude skill that helps you clarify and detail any project: an application, a thesis or paper, a research study. Instead of broad questions, the model asks a series of narrow ones and offers 2–4 ready-made solutions for each. You pick an option, add a comment to it, or write your own answer. At the end you get a Word document with everything that was worked out.

## How a session works

1. **Settings.** You choose the answer format ("choose an option" or "free-form answer") and the session length: 50, 100, 150 or 200 questions.
2. **Context.** You describe the project and attach materials: drafts, plans, requirements, code, papers.
3. **Interpretation.** The model restates how it understood the project and breaks the context into separate register items. Until you explicitly confirm an item by answering a question, it counts as unverified, even if it came from your own text.
4. **Rounds.** Each round has 5 narrow questions. The model picks them based on the state of the register: conflicts first, then decisions that other decisions depend on, then everything else. Before each round it briefly reports what was added, what changed and which conflicts appeared.
5. **Result.** A Word document: list of context files, initial interpretation, current understanding of the project, decision register, glossary, conflicts, open questions, risks, next steps and the full question-and-answer log.

## How to answer

- **Buttons** (if the interface supports them): pick an option. To give your own answer or refine an option, choose "Other" and write, for example: `b, but only for the pilot`.
- **As text:** `31b`, `31b: comment`, `31d: custom answer`. In free-form mode: `31: text`.

## Commands

| Command | Action |
|---|---|
| `summary` / `finish` | build the Word document and end the session |
| `show context` | current summary without new questions |
| `switch format` | change the answer mode |
| `focus <topic>` | devote the next round to a topic |
| `skip` | skip a round |
| `pause` | save an interim document to continue later |

Commands also work in the language of the session (for example, `итог` in a Russian session).

Every 50 questions the model saves an interim document on its own. Upload it to a new chat and the session picks up where it left off.

## Installation

- **Claude (web and app):** upload the zip archive with the folder of the version you want in the Skills section of the settings.
- **Claude Code:** copy the folder of the version you want to `~/.claude/skills/`.

To start, ask Claude something like "let's work through my project with decision-drill".
