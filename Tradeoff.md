# Tradeoffs

This document describes some important design decisions that were made while designing the Math Solution Grader API.

---

# 1. Hybrid System (Symbolic Engine + LLM)

## Decision

Use a hybrid approach where the symbolic engine is the primary grader and the LLM is used only when required.

## Alternatives Considered

* Pure LLM-based grading
* Pure rule-based grading

## Why I Chose This

A pure LLM solution is flexible but can hallucinate and becomes expensive at scale. A pure rule-based system is reliable but struggles with natural language responses.

The hybrid approach gives a good balance between reliability, flexibility, and cost.

## Remaining Risk

Some complex or highly ambiguous solutions may still be difficult to grade automatically.

---

# 2. Prefer Returning "UNCERTAIN" Instead of Guessing

## Decision

If the system confidence is low, return an uncertain response.

## Alternatives Considered

Always return a grading result.

## Why I Chose This

The assignment specifically mentions that the system should avoid hallucinations. Returning a wrong answer with high confidence can negatively affect the student's learning experience.

## Remaining Risk

A few correct answers may be marked as uncertain.

---

# 3. Generate Solutions Dynamically

## Decision

Generate the reference solution during grading.

## Alternatives Considered

Store official solutions in the database.

## Why I Chose This

The problem statement clearly mentions that official solutions are not available in the database. Therefore, the system needs to solve the problem itself.

## Remaining Risk

Some advanced questions may have multiple valid solution paths that are difficult to generate completely.

---

# 4. Use SymPy as the Primary Math Engine

## Decision

Use SymPy for symbolic mathematics.

## Alternatives Considered

* Build a custom solver
* Use only an LLM

## Why I Chose This

SymPy is reliable, open source, and supports many operations required for Grade 11 and Grade 12 mathematics.

## Remaining Risk

Some question types may require additional mathematical tools.

---

# 5. Use Stateless Services

## Decision

Keep grading services stateless.

## Alternatives Considered

Store session data inside application servers.

## Why I Chose This

Stateless services are easier to scale and can support the requirement of one million requests per day.

## Remaining Risk

Additional infrastructure such as Redis may be required for caching.

---

# 6. Minimize LLM Usage

## Decision

Call the LLM only when necessary.

## Alternatives Considered

Use the LLM for every request.

## Why I Chose This

The assignment has a strict cost limit of $0.008 per request. Using an LLM for every request could increase costs significantly.

## Remaining Risk

Some natural language responses may not receive the same quality of explanation.

---

# Final Thought

The main goal of this design is not to achieve perfect grading accuracy. Instead, the focus is to build a system that is reliable, scalable, cost-effective, and capable of failing safely when it is uncertain.
