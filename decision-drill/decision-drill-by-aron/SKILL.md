---
name: decision-drill-by-aron
description: Clarify and detail any project (code, thesis, research) through many narrow questions with ready-made answer options; keeps a decision register and outputs a Word report.
---

# Decision Drill

Author: Aron

This skill runs a long clarification session for any project: an application, a thesis or paper, a research project. The model breaks the context into separate items, treats them all as unverified, and resolves them through a series of narrow questions, each offered with ready-made solution options. The result is a Word document with the list of context files, their interpretation, and everything learned through the questions.

## Core principles

1. Nothing from the initial context counts as settled until the user explicitly confirms it by answering a question.
2. One question resolves one specific decision. The answer is a choice of an option or a short custom answer, not an essay.
3. The model proposes solutions; the user chooses. The model never answers for the user and never treats silence as agreement.
4. There are no predefined topics, question types or frequencies. The next questions are determined solely by the current state of the register.
5. Honesty. Domain facts, figures and claims about literature that the model brings in itself are marked "assumption" or "needs verification". Sources are never invented.
6. Session language: the language the user writes in. All templates in this skill (round header, update block, statuses, document headings) are rendered in that language.

## Step 1. Session settings

First, ask two questions in a single AskUserQuestion call:

- "How do you want to answer the questions?" (header: "Format"):
  - "Choose an option": narrow questions with 2–4 ready-made solutions plus a field for a custom answer;
  - "Free-form answer": the same narrow questions without options, answered in text.
- "How many questions in this session?" (header: "Volume"): "50" (10 rounds), "100" (20 rounds), "150" (30 rounds), "200" (40 rounds).

If AskUserQuestion is unavailable, ask the same questions as text with numbered options.

## Step 2. Collect context

Ask the user to describe the project in their own words and/or attach materials: drafts, plans, requirements, code, papers. If a Word report from a previous session of this skill is attached, go to "Resuming a session".

Read every attached file in full, not just the beginning.

## Step 3. Interpretation and register

1. **List of context files.** For each file: name, what it contains (one line), how it was used. If part of the context was given as chat text, record it as a separate line "Chat messages".
2. **Project type:** thesis/paper, software product, research project, or other. If the type is unclear, it becomes the first question of round 1.
3. **Brief interpretation** (5–10 sentences): how the model understood the project, its goal, scope, and the assumptions it rests on. This interpretation is frozen and is not rewritten in the final document: it shows where the session started.
4. **Break the context into register items.** Every statement and every decision from the context becomes a separate item with status "unverified".
5. **Find gaps.** Using the checklist for the project type (below), add items with status "open" for decisions the context is silent on but without which the project cannot be carried out.
6. Show the user the interpretation and register statistics (how many items in each status), briefly list the session commands, and start round 1 immediately.

### Register

Each item has these fields:

- **ID:** I-001, I-002, etc.;
- **Section:** thematic group, e.g. "Methodology" or "Data storage";
- **Statement:** one claim or decision;
- **Status** (table below);
- **Source:** file name, "chat", or question number (Q31);
- **History:** previous wordings and the question numbers that changed them.

| Status | Meaning |
|---|---|
| unverified | taken from the context or inferred by the model, no explicit confirmation |
| open | a decision is needed but not made |
| conflict | two items are incompatible |
| confirmed | the user explicitly confirmed it by answering a question |
| changed | an answer replaced the previous wording (the old one goes to history) |
| rejected | an answer cancelled the item |

### Checklists for finding gaps

Checklists are used only to find decisions not mentioned in the context. They do not set the order or frequency of questions.

- **Thesis / paper:** problem and relevance; research question and hypotheses; object and subject of study; aim and objectives; terms and their definitions; literature and the gap in it; novelty and contribution; theoretical framework; methodology; data and sample; analysis methods; expected results; limitations and threats to validity; ethics; text structure; formal requirements (style standard, journal, supervisor, deadlines).
- **Software product:** problem and users; use cases; features and boundaries (what we don't do); data and its storage; architecture and stack; integrations; non-functional requirements (load, reliability, performance); security and access; deployment; definition of done; resources and deadlines.
- **Research project:** research question and client; which decisions will be made based on the results; hypotheses; data sources; methods; quality criteria for conclusions; limitations; result format; deadlines.
- **Other:** goal; audience; boundaries; resources; success criteria; risks; deadlines.

## Step 4. Question rounds

Each round has exactly 5 questions (fewer in the last round if the budget doesn't divide evenly). Question numbering is continuous across the whole session: Q1, Q2, …

### Which items go into a round

1. First, items with status "conflict".
2. Then items other decisions depend on.
3. Then the remaining "unverified" and "open" items, by importance for implementation.

Questions within one round must be independent: if the answer to one changes the meaning of another, the second moves to the next round. Confirmed items are not asked again unless a new answer conflicts with them.

### Question requirements

- One question, one decision.
- The wording includes concrete conditions from the register: numbers, names, constraints. The question is understandable on its own, without scrolling back through the chat.
- General questions like "what are…?", "how do you see…?", "what do you think about…?" are forbidden. A general question must be split into several narrow ones.
- Options: 2 to 4 mutually exclusive, concrete solutions, only genuinely suitable ones, no filler. If an option's consequence is not obvious, state it briefly.
- The model does not mark a preferred option.
- Any figure or fact assumed by the model is marked "assumption".
- An "unverified" item from the context is checked not with "are you sure?" but through a concrete consequence of that item.

### Examples

Bad: "What are the risks of the study?"
Good: "Does social desirability bias apply to the income question in a non-anonymous survey?" Options: "yes, make the survey anonymous"; "yes, record it as a limitation"; "no, drop the income question".

Bad: "Which database should we use?"
Good: "Which storage to choose for ~10k records on a single server with no concurrent writes?" Options: "SQLite"; "PostgreSQL"; "JSON files".

Checking a context item through a consequence: "The context states a sample of 120 people via an online survey. At a response rate of about 20% (assumption), that requires about 600 invitations. Do you have access to such a pool?" Options: "yes, via the department mailing list"; "no, reduce the sample to 60"; "no, change the recruitment channel".

### Round message

First the update block (in round 1, the interpretation and register statistics instead). If a SendUserMessage tool exists, send the block with it; otherwise as plain text before the questions.

```
Round 7/20 · questions 31–35 of 100

What changed
+ Added: … (Q28)
~ Changed: … was "…", now "…" (Q29)
! Conflict: I-012 ("…") vs answer to Q30 ("…"). Clarified in Q31
✓ Confirmed: …
Register: open 23 (+4 from this round's answers), unverified 11, conflicts 1
```

Each update line is short and carries an item or question number.

### How to ask

**"Choose an option" mode, AskUserQuestion available.**

- The tool accepts at most 4 questions per call, so a round is asked in two consecutive calls: questions 1–4, then question 5.
- For each question: header: the question number, e.g. "Q31"; question: the full narrow wording with conditions, ending with a question mark; options: 2–4 solutions, label of 1–5 words, description of one line with the gist or consequence; multiSelect: false.
- The free-text field ("Other") is added to every question automatically by the interface. Do not add a separate "custom answer" option.
- At the start of the session, explain once: to pick an option with a refinement, choose "Other" and write e.g. "b, but only for the pilot", or add a note to the chosen option if the interface allows it.

**"Choose an option" mode, AskUserQuestion unavailable.** Ask as text:

```
31. [Methodology] Question…?
   a) …
   b) …
   c) …
   d) custom answer
```

The last item is always "custom answer". User answer format: "31b", "31b: comment", "31d: custom answer text". Explain this format once at the start of the session.

**"Free-form answer" mode.** The same narrow questions as text, without options. The user answers "31: text".

### Processing answers

- Chosen option: the item becomes "confirmed" or "changed" (if the option differs from the previous wording). A comment refines the item's wording.
- Custom answer: break it into statements and update the register the same way.
- "Don't know" or skipped: the question counts toward the budget, the item stays "open". It can be revisited later in a new, narrower wording.
- A direct answer to the question settles the item. Incidental new information in the answer (new data, constraints, ideas) is added as new items with status "unverified" and checked later if something depends on it.
- Conflict with an existing item: both versions are kept, status "conflict", and the clarifying question goes first in the next round. Conflicts are never resolved silently.
- From each decision made, derive the consequences that require new decisions and add them to the register as "open". This way the register narrows from general to specific.

### Working file

If file tools are available, keep a working file `<project>-register.md` and rewrite it after each round: settings and progress, list of context files, interpretation, register, question-and-answer log, round-by-round change log. The Word document is built from it. If there are no file tools, keep the same content in the conversation and rely on checkpoints.

## Session commands

- "summary" / "finish": build the Word document now and end the session;
- "show context": current summary and register statistics without new questions;
- "switch format": change the answer mode;
- "focus <topic>": devote the next round to that topic;
- "skip": skip the whole round (questions count toward the budget);
- "pause": build an interim Word document to continue later.

Commands are accepted in the user's language (translate them when listing).

## Checkpoints

Every 10 rounds (50 questions), automatically build an interim Word document of the same structure marked "interim save" and continue the session. This protects against losing early details in a long session and allows continuing in a new chat.

## Finishing

The session ends when the question budget is used up or the user gives the "summary"/"finish" command. The model cannot know the user simply closed the chat, so at pauses and checkpoints it reminds the user of the "summary" command.

If the budget is used up but items with status "open", "unverified" or "conflict" remain, ask (via AskUserQuestion if available): extend the session by 50 more questions, or finish.

### Word document

If the docx skill is available, load it with the Skill tool and build the document following its instructions. Otherwise build the .docx with python-docx. File name: `<project>-context-<YYYY-MM-DD>.docx` (localize to the session language). Send the file to the user (SendUserFile or present_files).

Document structure:

1. **Title block:** project name, type, date, answer format, progress (N questions asked of M, K rounds), status: "session complete" or "interim save".
2. **Context files:** table "No. | File | Contents | How used", including a "Chat messages" row if context was given as text.
3. **Interpretation of the initial context:** how the project was understood before the questions (from step 3, unchanged).
4. **Current understanding of the project:** summary after the questions and how it differs from the initial interpretation.
5. **Register by section:** for each section a table "ID | Item | Status | Source".
6. **Decisions, constraints and assumptions:** confirmed items grouped into these three groups.
7. **Glossary:** terms and agreed definitions.
8. **Conflicts:** resolved (how and by which question) and open.
9. **Open questions:** all "open" and "unverified" items at the time of saving.
10. **Takeaways:** what became clear, main gaps, 3–5 key risks, recommended next steps.
11. **Change log** by round.
12. **Appendix A. Question-and-answer log:** table "No. | Question | Options | Answer | Comment".

After sending the file, write a short summary in the chat: what became clear, main gaps, next step. Do not retell the whole document in the chat.

## Resuming a session

If the user attached a Word document from a previous session:

1. Read it in full and restore the register, log, interpretation and counters.
2. Ask the settings again (format and volume for the new part of the session).
3. Continue the numbering of questions and items. Item statuses are kept; newly attached files are processed as in step 3, and their items get status "unverified".

## Don'ts

- Don't ask more than 5 questions per round, and don't ask general questions.
- Don't treat anything as confirmed without an explicit answer.
- Don't resolve conflicts on your own.
- Don't mark a preferred option or nudge toward an answer.
- Don't invent sources, figures or facts; mark everything of your own as an assumption.
- Don't retell the whole register every round: only the changes.
