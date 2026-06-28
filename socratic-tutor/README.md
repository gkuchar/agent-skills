# Socratic Tutor

A Claude skill that runs an interactive tutoring mode. Instead of answering your questions, Claude guides you to the answer with its own questions, then quizzes you after every explanation to check that the concept actually stuck.

## What it does

- Withholds direct answers and asks guiding questions that lead you to work the problem out yourself.
- Reveals the answer only when you are genuinely stuck or explicitly ask for it.
- After any substantive explanation, automatically generates 3 short comprehension questions and waits for your response before grading.
- Grades your answers, explains why the correct ones are correct, and loops on the ones you miss with fresh questions on the same concept until you have it.

## Install

Place the `socratic-tutor/` folder (containing `SKILL.md`) in your Claude skills directory. The skill loads automatically once it is there.

## Use

Activate with the `socratic:` prefix on any question:

```
socratic: how does TCP handle congestion control?
```

The mode then governs the rest of the conversation. Exit any time with "exit socratic" or "stop tutoring", or just move on to an unrelated task.

## How a session flows

1. **Guide, not answer.** Claude opens with a question that isolates the first sub-step, then builds each follow-up on your last response, narrowing in on the gap.
2. **Auto-quiz.** After an explanation, Claude posts 3 short questions anchored to what it just covered, then stops and waits.
3. **Grade.** Correct answers get confirmed with a brief note on why. Incorrect ones are marked wrong, with the answer withheld for now.
4. **Mastery loop.** For each miss, Claude asks a new question on the same concept from a different angle, repeating until you get it, then summarizes what was reinforced and returns to normal tutoring.

### Example

```
You:    socratic: why is a hash table lookup O(1) on average but O(n) worst case?

Claude: Start with the average case. When you hash a key and it lands in a bucket,
        how many items do you expect to find in that bucket?

You:    Just one, if the keys are spread evenly.

Claude: Right, so the lookup is constant. Now picture the opposite: every key hashes
        to the same bucket. What does that bucket look like, and how long is a lookup?

You:    It's basically a linked list of all n items, so you scan all of them. O(n).

Claude: Exactly. The worst case is total collision, which degrades the table into a
        linear scan.

        Quick check on what we just covered:
        1. What assumption makes the average-case lookup constant time?
        2. What is the structure inside a single bucket once collisions pile up?
        3. If you doubled the keys but kept the table size fixed, what happens to
           average lookup time, and why?
```

## Configuration

Edit `SKILL.md` to change:

- **Prefix.** Swap `socratic:` for something shorter like `tutor:`.
- **Quiz format.** The quiz is plain inline text by default. Switch it to an interactive widget if you prefer.
- **Quiz cadence.** The auto-quiz fires after substantive explanations, not trivial one-line replies. Make it strictly every reply if you want no exceptions.

## Design notes

- The skill uses an explicit `socratic:` prefix rather than letting Claude infer when to tutor. Skills trigger on description matching and tend to under-fire on simple questions, so a prefix guarantees the mode activates exactly when you want it and gives you a clean on/off toggle.
- It pairs with `generating-technical-quiz`: that skill builds a one-time 15-question assessment over a provided text, while this one is a live tutoring loop with a lightweight 3-question check after each explanation.