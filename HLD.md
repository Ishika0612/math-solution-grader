# High Level Design (HLD)

## Objective

The goal of this system is to build a Math Solution Grader API for Grade 11 and Grade 12 students. The API should be able to understand a student's written solution, even if it is messy or partially structured, and return:

* Step-wise feedback (if possible)
* Overall correctness
* A short explanation for the student
* An uncertain response if the system is not confident enough

---

## High Level Architecture

```text
Frontend
    |
    v
Grading API
    |
    v
Grading Service
    |
    +----------------------+
    |                      |
    v                      v
Input Parser          Solution Generator
    |                      |
    |                      +---- SymPy Engine
    |                      +---- LLM Service
    |
    v
Step Evaluator
    |
    v
Confidence Checker
    |
    v
Response Builder
```

---

## Main Components

### 1. Grading API

This is the entry point of the system. It receives the problem statement and the student's solution and performs basic validation.

---

### 2. Input Parser

Student responses may contain equations, text, or both. The parser converts the response into individual steps that can be evaluated.

Example:

Student Input:

```text
3(x+2)=18
3x+6=18
3x=12
x=4
```

Parsed Steps:

```text
Step 1 → 3(x+2)=18
Step 2 → 3x+6=18
Step 3 → 3x=12
Step 4 → x=4
```

---

### 3. Solution Generator

The database does not contain official solutions, so the system generates its own reference solution.

I would use:

* SymPy for symbolic mathematics
* LLM only for reasoning and explanations

The symbolic engine is preferred because it is cheaper and more reliable than using an LLM for every request.

---

### 4. Step Evaluator

This component compares each student step with valid mathematical transformations and marks it as:

* Correct
* Incorrect
* Partially Correct
* Cannot Determine

---

### 5. Confidence Checker

Since AI systems can make mistakes, every grading result should have a confidence score.

If the confidence is low, the API should avoid giving a wrong answer and return:

```json
{
  "status": "UNCERTAIN",
  "message": "The solution could not be graded safely."
}
```

---

## Data Flow

1. Frontend sends the problem and student solution.
2. API validates the request.
3. Parser extracts the steps.
4. Solution Generator creates a reference solution.
5. Step Evaluator checks each step.
6. Confidence Checker calculates confidence.
7. Final response is returned to the frontend.

---

## Handling Requirements

### Scale

* Around 1 million requests per day.
* Stateless services can be scaled horizontally.

### Latency

* Most requests should finish within a few seconds.
* LLM calls are used only when required.

### Cost

* Symbolic grading is used for most questions to keep the cost below the given limit.

### Reliability

* The system should never guess when it is uncertain.
* Returning "UNCERTAIN" is better than returning an incorrect grade.

---

## Assumptions

* Only Grade 11 and Grade 12 mathematics questions are supported.
* Diagram-based questions are out of scope.
* Student answers are submitted in text form.
* Internet access is available for external model APIs if needed.
