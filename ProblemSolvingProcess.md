# Problem Solving Process

## Understanding the Problem

I started by reading the requirements carefully. The main challenge was that the database does not contain the official solution or the final answer. Therefore, the system needs to generate a reference solution and compare it with the student's response.

Another challenge is that student responses can be messy and may contain equations, text explanations, or a mixture of both.

---

## Identifying the Constraints

The assignment specifies several business constraints:

* Latency should be less than 30 seconds.
* Cost should be below $0.008 per request.
* The system should support around 1 million requests per day.
* Hallucinations should be minimized.

These constraints strongly influenced the design decisions.

---

## Exploring Possible Approaches

I considered three approaches:

### 1. Pure LLM-based grading

Pros:

* Flexible.
* Can understand natural language.

Cons:

* Expensive.
* Can hallucinate.
* Less reliable for mathematical correctness.

### 2. Pure Rule-based System

Pros:

* Reliable.
* Cheap.

Cons:

* Difficult to handle natural language responses.

### 3. Hybrid System

Pros:

* Reliable.
* Cost effective.
* Better handling of messy inputs.

I finally selected the hybrid approach.

---

## Final Design

The final design uses:

* SymPy as the primary grading engine.
* LLM only for reasoning and explanation generation.
* Confidence scoring to avoid unsafe grading.
* Graceful failure when the system is uncertain.

---

## Evaluation Strategy

To test the design, I created an evaluation dataset containing:

* Correct answers
* Incorrect answers
* Partially correct solutions
* Multiple valid solution paths
* Messy and unstructured responses

---

## Assumptions

* Only Grade 11 and Grade 12 mathematics questions are considered.
* Diagram-based questions are out of scope.
* Student responses are text-based.

---

## If I Had More Time

If I had more time, I would:

* Build a working prototype.
* Add OCR support for handwritten solutions.
* Improve support for proof-based questions.
* Create a larger evaluation dataset and measure grading accuracy.

---

## Use of AI Tools

During this assignment, I used AI tools such as ChatGPT for brainstorming ideas, refining documentation structure, and reviewing different design alternatives.

The final architecture, assumptions, trade-offs, and design decisions were made after understanding the problem requirements and evaluating different approaches myself.

AI tools were used only as an assistant and not as a replacement for the design process.

