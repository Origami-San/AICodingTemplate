---
name: refactor
description: Refactor and clean up existing code without changing its observable behavior — remove duplication, split long functions and files, reduce code smells, improve naming and control flow, in small verified steps. Use this skill whenever the user asks to "refactor", "clean up this code", "improve code quality", "reduce complexity", "split this file/component", says the code is messy, hard to read, or hard to change — or in Russian says "отрефактори", "почисти код", "приведи код в порядок", "упрости код", "разбей этот файл", "разбей компонент", "код слишком запутанный", "тут каша". Also use it before implementing a feature in code that is awkward to change.
license: MIT
metadata:
  source: "ciembor/agent-rules-books (MIT) — distilled from Martin Fowler's Refactoring; workflow inspired by elifiner/refactoring"
  version: "2.0"
---

# Refactoring

Improve the internal structure of code **without changing its observable behavior**.

This file is binding policy. `MUST` is binding, `SHOULD` is a strong default, `MUST NOT` is forbidden.

**Output language: Russian.** These instructions are in English for precision, but every reply, report, table, commit message and code comment you produce MUST be in Russian. Switch only if the user writes to you in another language.

## 0. Core rule

Structure and behavior never change in the same step. If behavior must change, say so out loud and make it a separate, announced edit. Never disguise a feature change or a bug fix as a refactoring.

## 1. Effort proportionality — decide this first

Match process to risk. Overhead MUST NOT exceed the cost of the change itself.

| Tier | When | Process |
|---|---|---|
| **Light** | Rename, dead code removal, extract one obvious helper, add a guard clause. Local, reversible, obviously safe. | Just do it. No plan, no analysis report, no milestones. |
| **Normal** | One file or one module, tests exist and pass. | Short analysis → confirm with user → transform loop → verify. |
| **Heavy** | Unclear behavior, no tests on a critical path, cross-module restructuring, wide blast radius. | Full process: written plan, characterization tests first, staged milestones, rollback point. |

When unsure of the tier, take the smallest reversible step and reassess. Do not front-load heavy process.

## 2. Workflow (Normal and Heavy tiers)

### Phase 1 — Analyze, do not edit

1. Read the code and establish what it currently does.
2. Record the baseline: how is this verified? Tests, type checker, linter, manual run. If nothing exists, say so explicitly — that changes the tier.
3. List the smells found, each with file, line, and the concrete friction it causes.
4. Score every finding on **two independent axes** (§2.1). Impact alone is not enough to decide.
5. Present the audit table and the proposed first step. Wait for approval before editing.

If the user asks for an audit ("проведи аудит", "что тут стоит почистить"), Phase 1 is the whole deliverable. Produce the table and stop. Do not edit anything, do not start with the first item because it looks easy.

### 2.1 Risk / benefit scoring

Two separate axes. Never collapse them into one score.

- **Выхлоп (benefit)** — what the user gets: less duplication, easier testing, a future change touching one file instead of four, fewer places to break.
- **Риск (risk)** — what could break if this edit goes wrong: how much behavior depends on this code, how well it is covered by tests, how far the blast radius reaches, how clear the current behavior is.

Default recommendation follows from the pair:

| | Низкий риск | Высокий риск |
|---|---|---|
| **Высокий выхлоп** | ✅ Делаем | ⚠️ Делаем осторожно — сначала тесты, мелкими шагами, отдельными коммитами |
| **Низкий выхлоп** | ✅ Делаем — дёшево | ❌ Не делаем |

The bottom-right cell is a hard stop: do not propose high-risk work with no payoff, even if the code offends you. The top-right cell always requires explicit user approval and Heavy tier process — never start it unprompted.

Assess risk for HIGH and MEDIUM findings only. For LOW findings, one line each is enough — scoring them wastes more effort than fixing them.

### 2.2 Audit report format

Answer in Russian, in this shape:

```
## 🔴 Высокий приоритет

**1. [Что не так — обычными словами]**
Где: путь/к/файлу.py:45-120
Чем мешает: [конкретная цена — что сейчас сложно сделать из-за этого]
Выхлоп: [что станет проще после правки]
Риск: [низкий/высокий] — [что именно может сломаться]
Рекомендация: ✅ Делаем / ⚠️ Делаем осторожно / ❌ Не делаем — [почему]

## 🟡 Средний приоритет
[то же самое]

## 🟢 Низкий приоритет
[по одной строке: что и где, без разбора рисков]

## С чего начать
[одна правка — та, что даёт максимум выхлопа при минимуме риска]
```

### Phase 2 — Transform loop

Repeat, one transformation at a time:

1. Pick ONE transformation. Never bundle.
2. Apply it.
3. Verify (Phase 3). If it fails, revert — do not patch forward.
4. Commit separately, with a message stating it is a refactoring.
5. Report what changed and propose the next step.

If a step is too big to verify, it is too big. Split it.

### Phase 3 — Verification gate

Before claiming any refactoring is done, you MUST:

1. Run the project's tests. Not "the tests should still pass" — run them.
2. Run the type checker and linter if the project has them.
3. Confirm the result is the same as the baseline from Phase 1.

If you cannot run verification, say so plainly and label the change unverified. Never assert that behavior is preserved without evidence. Never delete or weaken a failing test to make a refactoring pass.

## 3. Smell triage

Thresholds are **attention triggers**, not laws. Crossing one means look, not act.

| Smell | Trigger | Move |
|---|---|---|
| Long function | >50 lines, or mixes phases (parse / validate / compute / I/O) | Extract function per coherent phase |
| Deep nesting | >3 levels | Guard clauses, extract predicate |
| Duplication | Same logic in 3+ places | Extract shared behavior — not a `utils` dump |
| God file / class | >500 lines, or many reasons to change | Extract module or class along the reasons |
| Long parameter list | >4 params, or boolean flags switching behavior | Parameter object, split the function |
| Unclear names | Name describes mechanism, not intent | Rename first — bad names block everything else |
| Feature envy | Method mostly touches another object's data | Move it to the owning concept |
| Shotgun surgery | One change forces edits across many files | Centralize the knowledge, fix the boundary |
| Divergent change | One unit changes for unrelated reasons | Split responsibilities |
| Primitive obsession | Same primitive bundle repeated | Give the concept a named type |
| Hidden dependencies | Globals, singletons, ambient context | Make dependencies explicit |
| Middle man / speculative generality | Layer forwards without adding meaning; abstraction has one caller | Inline or delete it |

Counter-rules that prevent overcorrection:

- Do not abstract coincidental similarity. Duplication that will diverge is not duplication.
- Do not create micro-functions with no explanatory value.
- Do not replace a single honest conditional with indirection.
- Do not introduce polymorphism to avoid one small local branch.
- Do not create `utils`, `helpers`, or `common` as a default answer to duplication.
- Understandable duplication beats unclear shared code.

## 4. Preferred first moves

Rename badly named things → extract coherent functions → isolate side effects → split mixed responsibilities → move behavior to the owning concept → remove duplication → simplify conditionals.

Full Fowler catalog (composing methods, moving features, organizing data, conditionals, inheritance and generalization): **read `references/catalog.md` only when the codebase is class-based and the simple moves above are insufficient.** Do not load it for procedural, functional, or component-based code.

## 5. When NOT to refactor

Stop or decline when:

- The requested change is already easy to implement.
- The code rarely changes and is not blocking anything.
- Requirements for this area are unclear or in flux.
- The improvement is cosmetic.
- The next abstraction is not yet justified.
- Further cleanup would be speculative.

Perfect code is not the goal. Changeability is.

## 6. Forbidden patterns

- **Big-bang rewrite** — replacing a working subsystem wholesale, or rewriting before understanding current behavior.
- **Mixed-intent patch** — feature work bundled with unrelated renames; behavior changes hidden inside cleanup; code motion that makes review impossible.
- **Premature abstraction** — interfaces or strategy hierarchies before a second real need; a shared library for one caller.
- **Refactoring theater** — renaming while the real design problem stays untouched; adding patterns instead of removing complexity; more files, layers and wrappers with no gain in changeability.
- **Untested structural surgery** — large refactors with no safety net; assuming behavior is obvious when it is not.

## 7. Reporting to the user

Reply in Russian. Assume the reader may not read code fluently.

For every finding, state four things in plain words:

1. **What is wrong** — describe the problem itself, not its textbook name. "Эта функция делает пять разных вещей сразу", not "смелл Long Method".
2. **Why it hurts** — the concrete cost. Harder to test, one change breaks three places, a new developer needs an hour to follow it.
3. **What the fix buys** — the practical result. "После правки добавление способа оплаты трогает один файл вместо четырёх."
4. **What could break** — the risk, in the same plain words. "Здесь считается итоговая сумма заказа, тестов на неё нет — если ошибёмся, клиенты увидят неправильную цену."

Rules:

- Use English refactoring terminology (Extract Method, Feature Envy) only if the user has used such terms themselves, or ask first. Otherwise describe the move in ordinary words.
- Show before/after only for the fragment that changed, not whole files.
- Never report a refactoring as finished without saying how it was verified.
- If a finding is a matter of taste rather than a real problem, mark it as optional.

## 8. Final checklist

Before finishing, verify each:

- [ ] Observable behavior preserved, and verified by running something.
- [ ] Structural changes separated from behavior changes.
- [ ] At least one real source of friction removed.
- [ ] Reading and changing the code is easier than before.
- [ ] No speculative abstraction introduced.
- [ ] No giant mixed patch.
- [ ] Findings explained in language the user actually understands.

If any answer is no, revise before shipping.

When uncertain, choose the next small behavior-preserving transformation that makes the requested change easier. Reject approaches that gamble on large rewrites or mix several intentions at once.
