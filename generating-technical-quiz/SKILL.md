---
name: generating-technical-quiz
description: "Generate a quiz based on the provided technical article, book chapter, or textbook section. Use when 'technical quiz:' is encountered in the prompt."
---
 
# Technical Quiz
 
## Structure
Always generate exactly:
- 5 multiple choice questions (4 answer choices each)
- 2 true/false questions
- 5 short answer questions
- 3 vocabulary questions
## Sequence
Always present sections in this order:
1. Multiple Choice
2. True/False
3. Short Answer
4. Vocabulary
## Question Design
The goal of the technical quiz is to maximize reading comprehension of the provided text for the user, as well as to maximize understanding of the technical concepts from the provided text for the user. Balance factual (surface-level) and inference-based (deep-level) comprehension questions across multiple choice, true/false, and short answer sections. Prioritize questions that reinforce technical concepts and cement reading comprehension.
 
## Anti-Patterns
- Do not reveal correct answers through answer metadata (avoid patterns like always making the longest option the correct answer or repeatedly making the same letter the correct answer)
- Do not label individual questions with their question type category (eg. 'factual' or 'inference-based').
## Presentation
 
Always deliver the initial quiz as a single interactive widget rendered inline in the chat, never as plain markdown text. Before building it, load the visualizer's `interactive` module so the widget follows the current styling and API rules.
 
Build one widget containing all 15 questions, in the required Structure and Sequence:
- Multiple Choice (Q1-Q5): radio buttons, one group per question, 4 choices each.
- True/False (Q6-Q7): radio buttons with True / False.
- Short Answer (Q8-Q12): multi-line text fields.
- Vocabulary (Q13-Q15): single-line or multi-line text fields.
The widget collects answers only. It must NOT grade, score, mark answers correct or incorrect, or reveal any answer. Keep all answer state in memory; do not use any browser storage.
 
Include one Submit button. On submit, gather every answer and call the global `sendPrompt(text)` function to send them back to chat as plain, readable text, grouped by section with each answer tied to its question number. Send unanswered questions as blank (they are graded as incorrect). Format example:
 
```
Here are my quiz answers:
 
Multiple Choice
1. B
2. A
...
True/False
6. True
...
Short Answer
8. <user's text>
...
Vocabulary
13. <user's text>
...
```
 
Minimal skeleton for the submit handoff (the rest of the form is built from the generated questions):
 
```javascript
function submitQuiz() {
  const text = formatAnswers(answers); // build the text block shown above
  sendPrompt(text);                    // hands the answers to chat
}
```
 
Once the answers arrive in chat, the widget's job is finished. Do not render any further widgets. Proceed entirely in chat through Grading and the Socratic Loop.
 
## Grading
 
When the user submits their answers (received from the quiz widget as the text block above):
 
1. Grade all answers against the provided text's content
2. For each question, display the question number, the question, and the user's answer.
3. If the user's answer is correct,  indicate by adding 'correct' and a broad conceptual explanation next to the user's answer. If the user's answer is incorrect, only add 'incorrect' with no further detail yet next to the user's answer.
4. After grading all questions, if all the user's answers are correct, jump to step 5 of the 'Socratic Loop' sequence.  If the user's answers are not all correct, jump to step 5 of the 'Grading' sequence
5. For the first incorrect answer, ask one guiding question that steers toward the correct reasoning without revealing it. Stop and wait for the user's response, and then one the user's response is received, jump to step 1 of the 'Socratic Loop' sequence.
### Socratic Loop
1. If the user's response demonstrates understanding: confirm to the user that they have passed the current question and can move onto the next incorrect answer, briefly reinforce the concept, and then move to the next incorrect answer
2.  If not:  ask another guiding question on the same answer, wait again
3. Never reveal the correct answer directly: always guide the user to the correct answer for the user to find themselves
4. Repeat until all incorrect answers are resolved
5. If all answers are correct, congratulate the user and deliver a brief
  summary of the article's key technical concepts to reinforce learning