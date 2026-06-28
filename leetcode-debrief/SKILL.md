---
name: leetcode-debrief
description: "Debrief a completed LeetCode-style coding problem to turn a passing solution into durable understanding. Use whenever a prompt begins with 'leetcode debrief:'. The user's working solution (all test cases passing) follows the trigger, with their own Time and Space complexity analysis in a comment at the bottom. Trigger on 'leetcode debrief:' even if the rest of the prompt is just pasted code with no further instructions."
---

# LeetCode Debrief

Triggered when a prompt starts with `leetcode debrief:`. The user has already solved a LeetCode-style problem with all test cases passing and pasted their working solution. At the bottom of the solution, in a comment, is their own Time and Space complexity analysis.

The goal is to convert a passing solution into lasting understanding: a clear post-mortem of what they wrote, an honest check on whether they can do better, and active recall to lock it in. Do not just praise the code. Surface the reusable pattern and test retention.

## Sequence

Run these phases in order. Phases 3 and 4 can span multiple turns. Do not start the quiz until the optimality phase has resolved.

### Phase 1: Parse the input

Identify the solution code and the bottom complexity comment. Read the actual code rather than assuming what it does from the problem name.

Sanity-check correctness. The premise is "all tests passing," but if you spot a real bug or an edge case the tests likely missed, say so plainly before continuing. Do not invent problems that are not there.

If the bottom complexity comment is missing, the self-analysis step (Question 8) has nothing to grade. Tell the user, ask them to add their own Big-O before you continue, and explain that stating it themselves is the part that builds the skill. If they decline, proceed but note that Question 8 is being answered from scratch rather than checked against their work.

### Phase 2: The debrief

Answer all eight questions, each under its own short heading, in this order:

1. What did I do?
2. How did I do it?
3. Why did I do it?
4. How did it work?
5. Why did it work?
6. What did I learn?
7. How can I apply what I learned to new problems?
8. Is my time and space complexity analysis accurate? If not, explain why.

Keep each answer focused. Several of these overlap (what/how/why did I do it, and how/why did it work), so do not repeat content across them. Let each answer add something the previous one did not. Ground every answer in the user's actual code, referencing specific variables, data structures, and lines rather than generic descriptions of the problem.

Question 8 is narrow. Judge only whether the Big-O the user **wrote** is correct. This is about the accuracy of their self-assessment, not whether a faster solution exists. If their stated complexity is wrong, walk through the real cost (which loop dominates, which operation is hidden, what the recurrence is) until the correct bound is clear. Whether a **better** solution exists belongs in Phase 3, not here. Keep the two strictly separate, because conflating them either reveals the optimal approach early and kills Phase 3, or lets a wrong self-assessment slide.

### Phase 3: Optimality check

State whether the solution is optimal in time and in space. Judge each independently, since a solution can be time-optimal but space-heavy, or the reverse.

If it is optimal, say so and briefly justify why no better bound is reachable (for example, every element must be read at least once, so O(n) time is the floor).

If it is not optimal, do NOT reveal the better approach or name its key data structure. Give one targeted hint aimed at the specific gap, then stop and wait for the user's reply.

Then run the hint loop:

1. If the user's next message moves toward the optimal approach, confirm the progress, reinforce the insight in one or two sentences, and either nudge once more toward what is still missing or congratulate them once they have the full optimal idea.
2. If it does not move toward the answer, give another hint on the same gap from a different angle. Never hand over the optimal solution outright. The user finds it themselves.
3. End the loop when the user reaches the optimal approach, OR when they explicitly say the current solution is good enough. Respect "good enough" immediately, with no further nudging.

### Phase 4: The quiz

Only after Phase 3 has resolved, deliver a five-question short-answer quiz to cement the solution. Write questions that force recall of the mechanism and the transferable pattern, not surface trivia. Cover a spread:

- the core idea or invariant that makes the solution work
- the time or space complexity and why it is what it is
- the failure mode of a naive approach to this problem
- a transfer question: when would you reach for this pattern again
- one edge case or subtlety specific to this problem

Deliver the quiz as an interactive widget, matching the user's existing quiz workflow. Load the visualizer's `interactive` module first so the widget follows current styling and API rules. Build one widget with five short-answer text fields. The widget collects answers only: it must NOT grade, score, mark answers, or reveal anything, and must keep all state in memory with no browser storage. Include one Submit button. On submit, gather every answer and call the global `sendPrompt(text)` function to send them back to chat as plain text, numbered 1 through 5. Send unanswered questions as blank (graded as incorrect).

If the user would rather skip the widget for a quiz this short, plain numbered questions in chat are an acceptable substitute.

## Grading the quiz

When the answers arrive in chat, grade with a Socratic loop:

1. For each question, restate it with the user's answer. If correct, mark it correct and add a brief reinforcing note. If incorrect, mark it incorrect with no explanation yet.
2. If all five are correct, congratulate the user, give a two or three sentence summary of the reusable pattern, and end the session.
3. If any are incorrect, take the first incorrect answer and ask one guiding question that steers toward the right reasoning without revealing it. Stop and wait.
4. When the reply shows understanding, confirm it, reinforce the concept briefly, and move to the next incorrect answer. If it does not, ask another guiding question on the same answer from a different angle. Never state the correct answer outright.
5. Repeat until every answer is resolved, then close with the pattern summary.