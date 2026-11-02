# Networking Midterm — Study Materials (Lab 09)

Study materials for the networking midterm covering **chapters 1–5** of
*An Introduction to Computer Networks* (Peter L. Dordal).

> **Exam facts:** Friday. The professor said **all exam questions will be textbook exercises**,
> so everything here is organized around the exercise sections. A one-page cheat sheet is allowed.

## What's here

| Path | What it is |
|------|------------|
| `study-guide/study-guide.md` | The main guide: exercise census, topic-by-topic coverage, key facts |
| `problems/sample-problems.md` | Exercise-style practice questions (hints only) |
| `problems/practice-quiz.md` | Short self-test quiz (12 Qs) with answer key |
| `problems/worked-examples.md` | Full worked solutions to the sample problems |
| `flashcards/flashcards.md` | Human-readable flashcards, grouped by chapter |
| `flashcards/flashcards.csv` | Import into **Anki** (File → Import; fields: Front, Back, Tags) |
| `animations/` | Self-contained HTML animations (open `animations/index.html`) |
| `cheatsheet/cheatsheet.html` | **One-page** printable cheat sheet (Ctrl/Cmd-P → Letter) |
| `quiz/` | Reserved for the quiz done **without LLM assistance** |

## Quick start

1. **Read** `study-guide/study-guide.md` — start with the exercise census in §1.
2. **Practice** with `problems/sample-problems.md`, then check `problems/worked-examples.md`.
3. **Self-test** with `problems/practice-quiz.md` (short, answer key included).
4. **Drill** `flashcards/flashcards.csv` in Anki (make a deck, import, split on comma).
5. **Watch** `animations/csma-cd.html`, `ethernet-learning.html`, `spanning-tree.html`,
   `datagram-forwarding.html` (just open them in a browser).
6. **Print** `cheatsheet/cheatsheet.html` for the allowed one-page sheet.
7. **Quiz** — do it without LLMs; drop your answers in `quiz/`.

## Animations

Open `animations/index.html`, or the files directly:

- **CSMA/CD & Exponential Backoff** — 5 stations contend; backoff resolves collisions.
- **Ethernet Learning Switches** — send A→D, D→A, A→B, B→D and watch tables fill in.
- **Spanning Tree** — root election → root ports → pruned links.
- **Datagram Forwarding** — hop-by-hop delivery, plus an injected routing loop.

They are single files with no dependencies and can be served via GitHub Pages.

## Sources & licensing

Content is derived from the free textbook *An Introduction to Computer Networks* (2nd ed.) by
Peter L. Dordal, used under **CC BY-NC-ND**. See `ATTRIBUTION.md`. Our own study materials are
released under the same non-commercial terms.

## Team

Team **#1321** — Christian Dow (cjd1258@usnh.edu), Cameron Nell (cn1188@usnh.edu).
