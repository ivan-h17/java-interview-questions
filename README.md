# Java Interview Questions

Bilingual Java interview notes for Senior Java Backend preparation.

## Goals

- Prepare answers in Russian and English.
- Keep answers concise, practical, and interview-ready.
- Use pull requests for review.
- Use AI only for drafts; final answers must be reviewed by humans.

## Structure

- `docs/topics/` — topic notes.
- `docs/topics/<topic>/... Glossary RU-EN.md` — topic-local Russian/English terminology.
- New topic files may start as question-list skeletons and be expanded with reviewed answers over time.

## Question Lists

Target level: **Senior Java Backend Developer**

Question lists are English-first topic-local files under `docs/topics/<topic>/`, usually named like `<NN.00. Questions - Topic>.md`.

## Question Quality

A good question should be useful for a real Middle/Senior Java Backend interview. It should be relevant to backend work, recurring in interview sources or production practice, specific enough to answer well, and able to produce practical traps, caveats, trade-offs, or production angles.

Memorization is acceptable when it covers foundational vocabulary, common classifications, standard API behavior, or frequent interview entry points. Do not keep questions that are low-value trivia, too academic, duplicated by another question, unverifiable, or only DBA/platform-specialist depth.

## Legend

- Regular questions without an emoji = Priority 1 / required first-pass questions.
- 🧠 questions = Priority 2 / deep-dive questions for advanced preparation.
- The lists intentionally keep both levels:
  - definition questions: “What is it?”
  - deep-dive questions: “How does it work under the hood?”
  - production questions: “What can go wrong and how would you debug it?”

## Answer format

Each important question should have:

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

The question title is required in English only. Put it in the `<summary>` line; a separate Russian question field is not required.

For each answered question file, use the same collapsible two-column table format as the existing answer files:

```markdown
<a id="db-area-001"></a>

<details>
<summary><strong>DB-AREA-001. Question title in English?</strong></summary>

<table>
<tbody>
<tr>
<th colspan="2" align="left">Short answer</th>
</tr>
<tr>
<td valign="top" width="50%">
<p>Short answer EN.</p>
</td>
<td valign="top" width="50%">
<p>Short answer RU.</p>
</td>
</tr>
<tr>
<th colspan="2" align="left">Interview answer</th>
</tr>
<tr>
<td valign="top" width="50%">
<p>Interview answer EN.</p>
</td>
<td valign="top" width="50%">
<p>Interview answer RU.</p>
</td>
</tr>
<tr>
<th colspan="2" align="left">Deep dive</th>
</tr>
<tr>
<td valign="top" width="50%">
<p>Deep dive EN.</p>
</td>
<td valign="top" width="50%">
<p>Deep dive RU.</p>
</td>
</tr>
<tr>
<th colspan="2" align="left">Production angle</th>
</tr>
<tr>
<td valign="top" width="50%">
<p>Production angle EN.</p>
</td>
<td valign="top" width="50%">
<p>Production angle RU.</p>
</td>
</tr>
<tr>
<th colspan="2" align="left">Traps / caveats</th>
</tr>
<tr>
<td valign="top" width="50%">
<ul>
<li>Trap EN.</li>
</ul>
</td>
<td valign="top" width="50%">
<ul>
<li>Trap RU.</li>
</ul>
</td>
</tr>
<tr>
<th colspan="2" align="left">Optional Anki</th>
</tr>
<tr>
<td valign="top" width="50%">
<p>Q: ...<br>A: ...</p>
</td>
<td valign="top" width="50%">
<p>Q: ...<br>A: ...</p>
</td>
</tr>
</tbody>
</table>

</details>
```
