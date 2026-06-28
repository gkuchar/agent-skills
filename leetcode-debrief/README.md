# LeetCode Debrief

A Claude skill that turns a passing LeetCode-style solution into durable understanding. You bring a solution that already works; the skill walks you through what you did and why, checks whether you can do better without handing you the answer, and ends with a short quiz to lock it in.

It is the counterpart to a "give me a hint" workflow. Hints help you get unstuck before solving. This runs after you have solved, so the value is retention and pattern transfer rather than getting to green.

## When to use it

Right after you finish a problem with all test cases passing and want the solution to actually stick instead of fading by next week. It pairs naturally with spaced repetition: solve, debrief, revisit.

## How to use it

Start your message with `leetcode debrief:` then paste your working solution. Put your own Time and Space complexity analysis in a comment at the bottom of the code. The bottom comment matters: the skill grades your stated Big-O against reality, so leaving it out removes the part that builds the skill, and the skill will ask you to add it.

### Example

```
leetcode debrief:

class Solution:
    def twoSum(self, nums, target):
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
        return []

# Time: O(n^2), Space: O(1)
```

Here the complexity comment is accurate, so Question 8 passes, but the solution is not optimal, so the skill nudges you toward a hash map for O(n) without naming it. That is the two paths working at once.

## What it does

The session runs in four phases, then grading.

1. **Parse and sanity-check.** Reads your actual code, not the problem name. Flags a real bug or a missed edge case if it sees one, even though tests pass, since hidden tests are not exhaustive.
2. **The debrief.** Answers all eight reflection questions against your specific variables and lines: what you did, how, why, how it worked, why it worked, what you learned, how to apply it, and whether your stated complexity is correct.
3. **Optimality check.** Says whether you can do better in time and space, judged independently. If you can, you get one targeted hint at a time and have to find the better approach yourself. The loop ends when you reach it or say the current solution is good enough.
4. **The quiz.** Five short-answer questions covering the core idea, the complexity, the naive failure mode, a transfer question, and one problem-specific subtlety. Delivered as an interactive widget, graded with a Socratic loop where wrong answers get guiding questions instead of the answer.

## Design notes

A few choices are deliberate and worth knowing.

**Analysis accuracy and optimality are kept separate.** Question 8 asks only whether the Big-O you wrote is correct. Whether a faster solution exists is a different question handled later. Your O(n log n) analysis can be perfectly accurate while an O(n) solution still exists. Merging the two would either spoil the optimal approach early or let a wrong self-assessment slide.

**The quiz comes last.** Because the optimality phase is interactive and can run several turns, the quiz only fires once that resolves.

**Nothing is handed to you.** The optimality hints and the quiz grading both withhold the answer and steer you toward it, matching the Socratic style of the quiz and hint skills.

## Installation

Place the `leetcode-debrief/` folder, containing `SKILL.md`, alongside your other user skills. No dependencies.

## Related skills

- `leetcode-hint` for getting unstuck before you solve.
- `generating-technical-quiz` for active recall on articles and book chapters, which shares the same Socratic grading pattern used here.