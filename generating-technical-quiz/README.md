# Technical Quiz Skill

A [Claude skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) that turns passive technical reading into active recall. Point it at an article, book chapter, or textbook section, and it generates a structured 15-question quiz, collects answers through an interactive widget, grades them against the source material, and runs a Socratic dialogue on anything answered incorrectly.

Built as part of a structured self-study regimen for software engineering and machine learning. I use it to read primary sources actively (currently working through John Ousterhout's *A Philosophy of Software Design*) rather than re-reading and mistaking familiarity for understanding.

## The problem

Finishing a dense chapter and feeling like you understood it is not the same as understanding it. Recognition is cheap and recall is expensive, so re-reading inflates confidence without building durable knowledge. The reliable fix is retrieval practice: testing yourself forces the material out of memory, which is what actually strengthens it.

The friction is that writing good questions over what you just read, then grading yourself honestly, is tedious enough that nobody does it. This skill removes that friction. It generates the quiz, withholds the answers while you work, and only engages your reasoning after you commit to an answer.

## What it does

1. Invoke it with `technical quiz: <reading material>`.
2. It generates 15 questions in a fixed structure: 5 multiple choice, 2 true/false, 5 short answer, and 3 vocabulary, balanced across factual recall and inference.
3. It renders all 15 questions inline as a single interactive widget. The widget collects answers only. It never grades, scores, or hints.
4. On submit, the widget serializes every answer (blanks included) and hands them back to the chat as plain text.
5. Claude grades each answer against the source. Correct answers get a short conceptual explanation. Incorrect answers are flagged with no detail yet.
6. For each wrong answer, a Socratic loop kicks in: one guiding question at a time, steering toward the correct reasoning without revealing it, until you arrive there yourself.
7. Once everything resolves, it delivers a brief summary of the reading's key technical concepts to consolidate the session.

## Design decisions

The interesting work in a skill is not the prompt text, it is the constraints. Each decision below traded something away on purpose.

### Fixed quiz structure over adaptive generation

Every quiz is the same 15-question shape in the same order. The alternative was letting the model size the quiz to the material, which sounds smarter but makes output nondeterministic and hard to trust. A fixed contract means I always know what I am getting, the question count never balloons, and the comprehension coverage (surface-level facts plus inference) is guaranteed rather than hoped for. The cost is that a short article and a long chapter get the same treatment. For a study tool used daily, predictability won.

### Interactive widget over markdown text

The questions render as a real form, not a markdown list I would answer by typing freehand into chat. A markdown quiz would have been trivial to build, but it leaks answers (the model's framing of a question often telegraphs the answer when both sit in the same text block) and it makes answering clumsy. The widget keeps all answer state in memory, never touches browser storage, and submits through a single handoff. The tradeoff is a more complex presentation layer for a cleaner separation between answering and grading.

### Collection and grading are separated

The widget has one job: gather answers. It is explicitly forbidden from grading, scoring, or marking anything correct. All evaluation happens afterward, in chat, against the source text. This is single responsibility applied to an agent workflow. Grading logic living in the widget would have meant shipping the answer key to the client, which defeats the entire point. Keeping the two phases apart also means the grading step has the full source material and the model's reasoning available, which client-side logic would not.

### Guided discovery over answer reveal

When you get something wrong, the skill never tells you the answer. It asks one question, waits, evaluates your response, and either advances or asks again. The obvious alternative (show the correct answer and a paragraph explaining it) is faster and produces a worse outcome, because being handed the answer reproduces the same passive-reading failure the tool exists to fix. The loop is slower by design. Slow is the feature.

### Documented anti-patterns

The skill spec calls out failure modes to avoid, like making the longest option correct or repeating the same answer letter, and labeling questions by cognitive type. These are easy patterns for a model to fall into and they let a test-taker game the quiz without understanding anything. Writing them down as explicit prohibitions is cheaper than discovering the leakage later and treats the spec as a contract rather than a suggestion.

## Architecture

The skill is a single Markdown file with YAML frontmatter. The frontmatter `description` is the trigger contract: Claude loads the skill when it sees `technical quiz:` in a prompt, so the routing logic lives in natural language rather than code.

The body defines four things: the question structure and sequence, the question design philosophy (the factual / inference balance), the presentation rules (build the widget, load the visualizer module first, in-memory state only), and the two-phase interaction protocol (grading, then the Socratic loop). The skill ships a minimal JavaScript skeleton for the submit handoff and leaves the rest of the form generation to the model, which keeps the spec focused on intent instead of implementation.

The full interaction is effectively a small state machine: generate, collect, grade, then loop on incorrect answers until the set is exhausted. Phases are kept distinct so each one has exactly the context it needs and nothing it could misuse.

## Tradeoffs and limitations

- **Grading is model-judged, not key-based.** Short-answer and vocabulary responses are evaluated against the source text by the model, so grading quality tracks model quality. For factual recall over a provided text this is reliable; for genuinely ambiguous answers it is not infallible.
- **Fixed length is a deliberate constraint, not a universal fit.** Very short or very long readings get the same 15 questions. A future version could expose a difficulty or length parameter without giving up the determinism that makes the current contract trustworthy.
- **Single-source by design.** It quizzes one reading at a time and does not yet track performance across sessions or surface a spaced-repetition schedule, which is the natural next extension.

## Usage

```
technical quiz: <paste an article, chapter, or section here>
```

The skill handles the rest: it builds the widget, waits for your submission, grades, and walks you through anything you missed.

## Stack and context

Authored as a Claude Agent Skill (Markdown plus YAML frontmatter) with an embedded interactive widget for answer collection. Part of a broader set of personal tooling I build to study software engineering and ML more deliberately.