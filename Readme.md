# AI-Powered Math Solution Grader API

## Overview

This project proposes a Math Solution Grader API for Grade 11 and Grade 12 mathematics problems.

Students may submit their solutions in different formats such as:

* Equations
* Step-by-step working
* Plain text explanations
* A mixture of equations and text

The objective of the system is to evaluate these solutions and provide:

* Per-step feedback whenever possible
* Overall correctness of the solution
* A short explanation for the student
* An uncertain response when the system cannot grade safely

The system is designed keeping the given business constraints in mind, including latency, cost, scalability, and reliability.

---

# Use Case

A frontend application sends:

1. The original math problem.
2. The student's submitted solution.

The API processes the response and returns a grading result that can be shown directly to the student.

---

# Proposed Approach

The solution uses a **hybrid grading approach**:

### Symbolic Math Engine (Primary)

* SymPy
* Equation solving
* Expression simplification
* Derivatives and integrals

### LLM Service (Secondary)

* Understanding ambiguous responses
* Handling natural language explanations
* Generating student-friendly explanations

The symbolic engine is preferred because it is cheaper and more reliable than using an LLM for every request.

---

# API Endpoint

## POST /grade

### Sample Request

```json
{
  "problem_statement": "Solve 3(x+2)=18",
  "student_solution": "3x+6=18\n3x=12\nx=4"
}
```

### Sample Response

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

# Uncertain Response Example

```json
{
  "status": "UNCERTAIN",
  "message": "The system could not grade this solution safely.",
  "confidence": 0.43
}
```

---

# Assumptions

* Only Grade 11 and Grade 12 mathematics problems are supported.
* Diagram-based questions are out of scope.
* Student responses are text-based.
* The database does not store official solutions.

---

# Design Goals

### Reliability

The system should avoid hallucinations and should not provide confident answers when it is uncertain.

### Latency

Most grading requests should finish within a few seconds and remain under the 30-second limit.

### Cost

The design keeps LLM usage minimal to satisfy the cost constraint.

### Scalability

The system should support around one million grading requests per day using stateless services and horizontal scaling.

---

# Future Improvements

* Support for additional mathematics topics.
* Better handling of multiple valid solution paths.
* Handwritten solution support using OCR.
* Personalized feedback and hints.
* Fine-tuned mathematical reasoning models.

---

# Known Limitations

* Graph drawing and geometric constructions are not supported.
* Extremely ambiguous student responses may return an uncertain result.
* Some advanced proof-based questions may require human review.

---

# Repository Structure

```text
math-solution-grader/
├── README.md
├── HLD.md
├── LLD.md
├── EvaluationSet.md
├── ProblemSolvingProcess.md
└── Tradeoff.md
```

---

# Conclusion

This project focuses on building a practical, reliable, and cost-effective grading system that can evaluate mathematical solutions while gracefully handling uncertainty.
