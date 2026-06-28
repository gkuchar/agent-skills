# educational-agent-skills

A collection of custom **AI agent skills** for technical learning and interview preparation, built on Anthropic's Claude Skills framework. Each skill encodes a deliberate pedagogical strategy (active recall, Socratic questioning, structured debriefs) into a reusable, interactive agent workflow.

Built by [Griffin Kuchar](https://github.com/gkuchar) to make self-study measurably more effective. Instead of passively consuming material, these skills turn an LLM into an active tutor that probes understanding, withholds answers until you've reasoned, and grades your thinking rather than your recall.

---

## Why this exists

Most people use LLMs as answer machines. That's the wrong tool for *learning*, because handing someone a solution short-circuits the struggle that actually builds understanding.

These skills invert that. They are designed around a single principle: **the learner does the thinking, the agent guides the process.** Each one enforces a research-backed learning technique through structured prompting, interactive widgets, and Socratic grading loops, so the agent coaches instead of solves.

---

## Skills

### `generating-technical-quiz`
Generates an interactive comprehension quiz from any technical article, book chapter, or paper. Presents questions through interactive widgets, then runs a **Socratic grading loop**: instead of marking an answer right or wrong, it asks follow-up questions that expose gaps in reasoning before confirming understanding. Used for active reading of software-engineering and ML material.

### `leetcode-tutor`
Guides a learner through a coding problem *without giving away the solution*. Surfaces the relevant patterns, asks leading questions about approach and tradeoffs, and only reveals code once the learner has reasoned through the logic, mirroring how a strong mentor actually teaches problem-solving.

### `leetcode-debrief`
Runs a structured post-mortem after solving a problem: time/space complexity analysis, alternative approaches, edge cases missed, and pattern generalization, converting a single solved problem into transferable knowledge.

### `socratic-tutor`
A general-purpose tutoring skill that teaches any concept through questioning rather than explanation, calibrating depth to the learner's demonstrated level and refusing to let the learner copy-paste their way past genuine understanding.

---

## Design philosophy

Each skill is built around a few deliberate engineering choices:

- **Process over answers.** The agent's job is to make the learner reason, not to produce output. Skills are explicitly structured to withhold solutions until understanding is demonstrated.
- **Socratic grading.** Responses are evaluated through follow-up questioning loops rather than binary correctness, surfacing *why* an answer is right or wrong.
- **Interactive presentation.** Questions and feedback are delivered through interactive widgets for a cleaner, more engaging learning loop than plain text.
- **Reusable structure.** Each skill is a self-contained, parameterized workflow that generalizes across topics and inputs.

---

## Tech

- **Anthropic Claude Skills** framework (`SKILL.md` skill definitions)
- Structured agentic prompting and tool-use patterns
- Interactive widget-based question presentation and grading
