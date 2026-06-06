# AGENTS.md

## Project goal

This repository contains bilingual Java interview notes for Senior Java Backend preparation.

## Question sources

Use these sources to discover question topics and coverage gaps. Do not copy long verbatim answers from them; write original answers in this repository's format.

Primary sources:

- https://github.com/enhorse/java-interview/tree/master
- https://github.com/DEBAGanov/interview_questions
- `questions/Top-20-topics-middle-java-RU.pdf`

Additional public sources for senior-level coverage:

- https://github.com/teamlead/java-interview-questions
- https://viacheslavchernyshov.github.io/java-interview-questions-and-answers/
- https://www.datacamp.com/blog/java-interview-questions
- https://blog.emb.global/senior-java-developer-interview-questions/
- https://startup.jobs/interview-questions/senior-java-developer
- https://easyinterview.me/blogs/the-interview-questions-that-matter/complete-java-backend-developer-interview-guide
- https://www.springjavalab.com/2025/12/system-design-interview-questions-java-microservices.html
- https://www.algoroq.io/interview-questions/system-design/

When prompted to generate or expand question lists, agents should search the internet for additional current question sources and popular Senior Java Backend interview questions. Prefer recurring topics from multiple sources over isolated lists.

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

## Question quality requirements

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
- `🧠` = Priority 2 / advanced, detailed, internals-heavy, or second-pass question.
- Basic questions can be Priority 1 if they are foundational and a weak answer would hurt a Middle/Senior interview.
- Use `🧠` for questions that are useful but deeper than normal interview readiness: low-level internals, implementation details, specialized tuning, rare edge cases, or tooling details.

Do not add or keep a question when:

- it is low-value memorization that does not lead to explanation, trade-offs, examples, or production relevance;
- it is too academic for a backend interview;
- it is only DBA/platform-specialist depth and not normally expected from a Java backend engineer;
- it is version-specific or fact-sensitive and cannot be verified;
- it overlaps strongly with another question and can be merged;
- it cannot produce useful traps, caveats, or production angle.

When reviewing a question list, agents should:

- check for duplicates and near-duplicates;
- mark missing high-value topics;
- classify questions as Priority 1 or `🧠`;
- remove or merge low-value questions;
- use `TODO: verify` if a technical detail needs checking;
- prefer current, recurring interview signals over isolated blog-list questions.

## Required format for each answered question

Every answered question should contain:

- Question EN
- Short answer EN
- Short answer RU
- Interview answer EN
- Interview answer RU
- Deep dive EN
- Deep dive RU
- Production angle EN
- Production angle RU
- Traps / caveats
- Optional Anki cards

The question title is required in English only. Do not add a separate Russian question field unless explicitly requested.

## Language rules

- Russian answers should be clear and natural.
- English answers should sound natural in an interview.
- Do not translate word-for-word.
- Prefer common English engineering terms.
- Important technical terms in Russian answers should include the common English term in parentheses when useful, for example `согласованность (consistency)` or `обратное давление (backpressure)`.
- Add terminology to the relevant topic glossary file when useful.
- Prefer per-topic glossary files, for example `docs/topics/18. Databases, SQL, PostgreSQL/18.00. Glossary RU-EN.md`.
- If a topic does not have a glossary yet, create one in that topic folder instead of using a repository-wide glossary.

## Answer style

Target level: Senior Java Backend Developer.

Good answers should be:

- concise but not shallow;
- practical;
- explainable aloud in 1–2 minutes;
- focused on backend development;
- include common traps and caveats;
- include examples when useful.

## Important topics

- Java Core
- Collections
- HashMap / equals / hashCode
- Multithreading
- Stream API
- Exceptions
- Spring / Spring Boot
- Transactions
- Hibernate / JPA
- PostgreSQL / SQL
- Kafka
- REST / HTTP
- Microservices
- Docker / Kubernetes
- Java Core language fundamentals
- Algorithms / coding interview basics
- Testing
- Spring Security / application security
- System Design basics
- Redis / caching
- Observability / monitoring / alerting
- Production troubleshooting
- Database migrations / schema evolution
- HTTP client resilience
- Event-driven architecture
- CI/CD
- Maven / Gradle / dependency management
- Git / code review
- Configuration / secrets / feature flags
- DDD / domain modeling
- JVM diagnostics / profiling
- JSON / Jackson / serialization
- Messaging alternatives and protocols
- Cloud fundamentals
- Linux / networking basics
- Search / Elasticsearch / OpenSearch
- NoSQL fundamentals

## Do not

- Do not copy long verbatim answers from third-party repositories.
- Do not invent facts.
- Do not make answers too academic.
- Do not write huge textbook-style answers.
- Do not skip traps and edge cases.
- Do not mark an answer as ready if it was not reviewed.

## Mark uncertainty

Use:

`TODO: verify`

when a technical detail needs checking.

## Review checklist

Before finalizing an answer:

- EN answer is natural.
- RU answer is understandable.
- The answer can be spoken in 1–2 minutes.
- Common traps are included.
- Technical details are checked.
- The answer is useful for a real Java backend interview.
