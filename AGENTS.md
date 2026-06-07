# AGENTS.md

## Project Goal

This repository contains Java interview notes for Senior Java Backend preparation.

## Question Sources

Use these sources to discover question topics and coverage gaps. Do not copy long verbatim answers from them; write original answers in this repository's format.

Primary sources:

- https://github.com/enhorse/java-interview/tree/master
- https://github.com/DEBAGanov/interview_questions

Additional public sources for senior-level coverage:

- https://github.com/teamlead/java-interview-questions
- https://viacheslavchernyshov.github.io/java-interview-questions-and-answers/
- https://www.datacamp.com/blog/java-interview-questions
- https://blog.emb.global/senior-java-developer-interview-questions/
- https://startup.jobs/interview-questions/senior-java-developer
- https://easyinterview.me/blogs/the-interview-questions-that-matter/complete-java-backend-developer-interview-guide
- https://www.springjavalab.com/2025/12/system-design-interview-questions-java-microservices.html
- https://www.algoroq.io/interview-questions/system-design/

When prompted to generate or expand question lists, search for additional current question sources and popular Senior Java Backend interview questions. Prefer recurring topics from multiple sources over isolated lists.

Current senior-level topic signals to cross-check:

- system design and architecture trade-offs;
- Java Core internals, collections, concurrency, JVM, GC;
- Spring Boot, Spring internals, transactions, security;
- Hibernate / JPA performance, fetching, locking, persistence context;
- SQL / PostgreSQL, indexes, query plans, MVCC, transactions;
- Kafka, event-driven architecture, idempotency, retries, ordering;
- REST / HTTP, API design, backward compatibility;
- microservices, distributed failures, consistency, observability;
- Redis / caching, cache invalidation, rate limiting;
- Docker / Kubernetes, CI/CD, cloud basics;
- practical coding, debugging, and production incident scenarios.

## Question Quality

A question is good for this repository when it helps a Middle/Senior Java Backend candidate explain a real engineering concept clearly in an interview. It should satisfy most of these checks:

- it is relevant to Java backend work, production systems, debugging, architecture, data correctness, performance, maintainability, or common framework behavior;
- it is likely to appear in real Middle/Senior interviews or covers a recurring topic from multiple sources;
- it can be answered aloud in 1-2 minutes without becoming a textbook chapter;
- it has practical traps, caveats, trade-offs, or production failure modes worth discussing;
- it is specific enough to answer well, but not so narrow that it becomes trivia;
- it may require memorization when the fact is foundational vocabulary, a common classification, a standard API behavior, or a frequent interview entry point;
- it fits the required answer format: short answer, interview answer, deep dive, production angle, traps/caveats;
- it does not duplicate an existing question unless it tests a clearly different angle.

Default priority rules:

- No emoji = Priority 1 / required first-pass question.
- Brain-marked question = Priority 2 / advanced, detailed, internals-heavy, or second-pass question.
- Basic questions can be Priority 1 if they are foundational and a weak answer would hurt a Middle/Senior interview.
- Use the brain marker for questions that are useful but deeper than normal interview readiness: low-level internals, implementation details, specialized tuning, rare edge cases, or tooling details.

Do not add or keep a question when:

- it is low-value memorization that does not lead to explanation, trade-offs, examples, or production relevance;
- it is too academic for a backend interview;
- it is only DBA/platform-specialist depth and not normally expected from a Java backend engineer;
- it is version-specific or fact-sensitive and cannot be verified;
- it overlaps strongly with another question and can be merged;
- it cannot produce useful traps, caveats, or production angle.

When reviewing a question list:

- check for duplicates and near-duplicates;
- mark missing high-value topics;
- classify questions as Priority 1 or brain-marked Priority 2;
- remove or merge low-value questions;
- use `TODO: verify` if a technical detail needs checking;
- prefer current, recurring interview signals over isolated blog-list questions.

## Answer Format

Every answer file should use collapsible question blocks separated by horizontal rules.

Each answered question should contain:

- Question title
- Short answer
- Interview answer
- Deep dive
- Production angle
- Traps / caveats
- Optional Anki cards

Use this shape:

```markdown
---

<a id="db-area-001"></a>

<details>
<summary><strong>DB-AREA-001. Question title?</strong></summary>

#### Short answer

Short answer.

#### Interview answer

Interview-ready answer.

#### Deep dive

Deeper explanation when useful.

#### Production angle

How this matters in production systems.

#### Traps / caveats

- Common trap.
- Common caveat.

#### Optional Anki

Q: ...
A: ...

</details>

---
```

Format rules:

- Keep question titles in `<summary>` so answer files remain navigable with clickable questions and collapsible answers.
- Put a stable `<a id="..."></a>` anchor before each question block.
- Put `---` before the first question, between every question, and after the last question.
- Use plain Markdown inside answer blocks.
- Do not use a top question index.
- Do not use HTML tables.
- Do not use two-column answer layouts.
- Do not add duplicate translated sections, glossary files, or translated section names.

## Answer Style

Target level: Senior Java Backend Developer.

Good answers should be:

- concise but not shallow;
- natural in an interview;
- explainable aloud in 1-2 minutes;
- focused on backend development;
- practical and production-aware;
- clear about trade-offs;
- explicit about common traps and caveats;
- supported by examples when useful.

Terminology rules:

- Keep Java identifiers, API names, exception names, tool names, acronyms, and common engineering terms in their standard form, for example `JVM`, `JDK`, `JIT`, `GC`, `JAR`, `API`, `CI`, `DTO`, `ORM`, `HashMap`, `equals()` or `NullPointerException`.
- Avoid vague textbook prose. Prefer concise explanation, concrete examples, trade-offs, and production implications.

## Do Not

- Do not copy long verbatim answers from third-party repositories.
- Do not invent facts.
- Do not make answers too academic.
- Do not write huge textbook-style answers.
- Do not skip traps and edge cases.
- Do not mark an answer as ready if it was not reviewed.
- Do not create glossary files.
- Do not add HTML tables or two-column answer layouts.

## Mark Uncertainty

Use:

`TODO: verify`

when a technical detail needs checking.

## Review Checklist

Before finalizing an answer:

- The answer reads naturally in an interview.
- The answer can be spoken in 1-2 minutes.
- Common traps are included.
- Technical details are checked.
- The answer is useful for a real Java backend interview.
