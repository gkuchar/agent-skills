# LeetCode Hint

A Claude skill that gives one small, conceptual hint on a stuck LeetCode-style attempt without revealing the solution or any code.

## What it does

When you are stuck on a coding problem, paste your incorrect attempt and the skill assesses it, identifies the single most fundamental thing blocking you, and gives one small nudge in the right direction. It targets the underlying conceptual or algorithmic mistake rather than surface bugs, and it never writes the solution or code for you.

## Usage

Start a message with `leetcode hint:` followed by the problem and your current attempt.

```
leetcode hint:

Problem: return the indices of the two numbers that add up to target.

def two_sum(nums, target):
    for i in range(len(nums)):
        for j in range(len(nums)):
            if i != j and nums[i] + nums[j] == target:
                return [i, j]
```

The skill replies with a single hint, for example:

> Your nested loop produces correct answers, but its cost grows too fast as the input gets large. What could you remember as you scan the array once so that you avoid recomputing the same comparisons?

It then stops and waits. If you reply that you are still stuck, it escalates by one notch and gets more concrete, still without code.

## What to expect

- **One hint per turn.** It gives a single nudge, then waits for your response.
- **Conceptual first.** It points at the real mistake (wrong approach, wrong data structure, wrong complexity class, a missed category of input), not typos or off-by-one errors.
- **Approach-aware.** A brute force that returns correct answers but is too slow for the constraints is treated as a conceptual problem, not a finished solution. If your approach is sound and only a small execution bug remains, it tells you the strategy is right so you stop re-deriving the algorithm.
- **No solution, no code.** The complete worked solution and any code stay with you, no matter how many times you come back. Hints escalate; the answer is never handed over.
- **Debugging on request.** Ask for debugging help explicitly and surface-level bugs come into scope. Even then it points you at the failing region rather than writing the fix.

## Installation

Install `leetcode-hint.skill` through the skills settings in the Claude app. Once installed it is available in any chat. Trigger it with the `leetcode hint:` prefix.

## Customization

The behavior is controlled by `SKILL.md` and is easy to adjust:

- **Strict single hint:** remove the escalation paragraphs in the Hint Sizing section so it does not get more concrete on repeat attempts.
- **Allow naming the technique up front:** loosen the rule that forbids naming the canonical pattern on the first hint.
- **Stay fully silent on surface-only bugs:** drop the edge-case paragraph in the Conceptual First section.

## Files

- `SKILL.md`: the skill definition and behavior rules
- `README.md`: this file