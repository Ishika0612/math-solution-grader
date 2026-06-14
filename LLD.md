# Low Level Design (LLD)

## 1. API Endpoint

### POST /grade

This endpoint receives a math problem and the student's solution and returns the grading result.

---

## Request Schema

```json
{
  "problem_id": "12345",
  "problem_statement": "Solve 3(x+2)=18",
  "student_solution": "3x+6=18\n3x=12\nx=4",
  "metadata": {
    "grade": 11,
    "subject": "Algebra",
    "language": "en"
  }
}
```

### Required Fields

* problem_statement
* student_solution

### Optional Fields

* problem_id
* metadata

---

## Response Schema

```json
{
  "overall_correct": true,
  "confidence": 0.95,
  "steps": [
    {
      "step": "3x+6=18",
      "status": "CORRECT"
    },
    {
      "step": "3x=12",
      "status": "CORRECT"
    },
    {
      "step": "x=4",
      "status": "CORRECT"
    }
  ],
  "explanation": "Your solution is correct."
}
```

---

## Uncertain Response

```json
{
  "status": "UNCERTAIN",
  "message": "The system could not grade this solution safely.",
  "confidence": 0.42
}
```

---

# Internal Modules

## 1. Request Validator

Responsibilities:

* Check missing fields
* Validate input length
* Remove unwanted characters
* Prevent empty submissions

---

## 2. Input Parser

Responsibilities:

* Split solution into steps
* Identify equations and text
* Normalize mathematical expressions

Example:

Input:

3x+6=18
3x=12
x=4

Output:

["3x+6=18", "3x=12", "x=4"]

---

## 3. Solution Generator

Responsibilities:

* Generate reference answer.
* Solve equations using SymPy.
* Generate alternative valid paths if possible.

---

## 4. Step Evaluator

Responsibilities:

* Compare student steps.
* Check mathematical equivalence.
* Detect partially correct work.

Possible labels:

* CORRECT
* INCORRECT
* PARTIALLY_CORRECT
* CANNOT_DETERMINE

---

## 5. Confidence Engine

Confidence is calculated using:

1. Parser confidence
2. Solver confidence
3. Number of matched steps
4. Agreement between symbolic engine and LLM.

Example:

```text
Final Confidence =
0.4(Parser)
+0.4(Step Matching)
+0.2(LLM Agreement)
```

---

## Grading Workflow

1. Receive request.
2. Validate input.
3. Parse student solution.
4. Generate reference solution.
5. Compare each step.
6. Calculate confidence.
7. Generate final response.

---

## Error Handling

### Invalid Request

```json
{
  "error": "Problem statement is missing."
}
```

### Unsupported Problem

```json
{
  "status": "UNSUPPORTED",
  "message": "This type of question is currently not supported."
}
```

### Internal Failure

```json
{
  "status": "ERROR",
  "message": "Something went wrong while grading."
}
```

---

# Logging and Observability

For debugging purposes, the following information should be logged:

* Request ID
* Processing time
* Confidence score
* Failure reason
* API errors

No student personal information should be stored in logs.

---

# Assumptions

* Student answers are text based.
* Diagram questions are out of scope.
* Symbolic mathematics covers most Grade 11 and 12 questions.
* LLM is used only when necessary to reduce cost and hallucinations.
