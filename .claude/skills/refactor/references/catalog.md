# Refactoring Catalog

Load this file only when the simple moves in SKILL.md §4 are insufficient — typically in class-based codebases (Java, C#, Python OOP, PHP, Ruby, Kotlin). Most of the inheritance section is irrelevant to procedural, functional, or component-based code.

Contents:
1. Composing Methods
2. Moving Features
3. Organizing Data
4. Simplifying Conditionals
5. Simplifying Method Calls
6. Generalization and Inheritance
7. Big Refactorings

---

## 1. Composing Methods

- **Extract Method** — a fragment has a coherent purpose and deserves a name.
- **Inline Method** — the body is clearer than the indirection.
- **Inline Temp** — a temporary obscures a direct expression.
- **Replace Temp with Query** — a calculated value deserves a named query and can be reused safely.
- **Introduce Explaining Variable** — a complex expression needs named parts.
- **Split Temporary Variable** — one variable carries multiple meanings.
- **Remove Assignments to Parameters** — parameter mutation obscures input meaning.
- **Replace Method with Method Object** — local state prevents clean extraction.
- **Substitute Algorithm** — a clearer algorithm replaces a tangled one, same behavior.
- **Split Loop** — one loop does two unrelated things.

## 2. Moving Features

- **Move Method / Move Field** — behavior or state belongs to another object.
- **Extract Class** — one class has more than one reason to change.
- **Inline Class** — a class no longer earns its existence.
- **Extract Module** — one file mixes unrelated concerns.
- **Hide Delegate** — clients know too much about a collaborator.
- **Remove Middle Man** — a forwarding object hides nothing useful.
- **Introduce Foreign Method** — only when you cannot edit the class that should own the behavior.
- **Introduce Local Extension** — repeated foreign methods need a coherent extension point.

## 3. Organizing Data

- **Self Encapsulate Field** — direct field access blocks flexibility.
- **Replace Data Value with Object** — a primitive carries behavior, validation, or meaning.
- **Replace Magic Value with Named Constant** — an unexplained literal appears in logic.
- **Change Value to Reference** — identity and shared updates matter.
- **Change Reference to Value** — value semantics simplify ownership.
- **Replace Array with Object** — positions in a collection have names or rules.
- **Encapsulate Collection** — external mutation can bypass invariants.
- **Replace Record with Data Class** — raw records need named access and room to grow.
- **Replace Type Code with Class / Subclasses / State / Strategy** — depending on whether behavior varies by type.
- **Replace Subclass with Fields** — subclass variation is only data.
- **Change Unidirectional Association to Bidirectional** — only when traversal is genuinely needed both ways.
- **Change Bidirectional Association to Unidirectional** — one direction is unnecessary coupling.
- **Duplicate Observed Data** — only when UI or framework synchronization forces it; keep sync explicit.

## 4. Simplifying Conditionals

- **Decompose Conditional** — make branching intent visible.
- **Consolidate Conditional Expression** — several checks lead to the same result.
- **Consolidate Duplicate Conditional Fragments** — identical code in every branch.
- **Replace Nested Conditional with Guard Clauses** — clarifies the normal path.
- **Remove Control Flag** — loop or conditional state can be expressed directly.
- **Replace Conditional with Polymorphism** — only when repeated type-based behavior justifies it.
- **Introduce Null Object** — repeated null handling has a stable meaning.
- **Introduce Assertion** — an assumption should be explicit.
- **Replace Conditional with Lookup Table** — stable mapping logic.

## 5. Simplifying Method Calls

- **Rename Method** — the name describes mechanism instead of behavior.
- **Add / Remove Parameter**, **Parameterize Method**, **Replace Parameter with Explicit Methods** — make caller intent clearer.
- **Preserve Whole Object** — callers pass several values from the same object.
- **Replace Parameter with Method** — the receiver can obtain the value itself without hidden coupling.
- **Introduce Parameter Object** — a repeated argument clump has a name.
- **Remove Setting Method** — post-construction mutation should not be allowed.
- **Hide Method** — the public surface exposes unnecessary operations.
- **Replace Constructor with Factory Method** — creation intent or subtype selection needs a name.
- **Replace Error Code with Exception** / **Replace Exception with Test** — match the expected failure model.
- **Encapsulate Downcast** — callers should not own cast details.

## 6. Generalization and Inheritance

Apply only where a real class hierarchy exists and variation is genuine. Do not introduce a hierarchy to satisfy this list.

- **Pull Up Field / Method / Constructor Body** — duplicated superclass behavior is real.
- **Push Down Method / Field** — only some subclasses need the feature.
- **Extract Subclass / Superclass / Interface** — only when callers or variation points justify them.
- **Collapse Hierarchy** — inheritance no longer adds meaning.
- **Form Template Method** — similar algorithms differ in controlled steps.
- **Replace Inheritance with Delegation** — inheritance couples unrelated responsibilities.
- **Replace Delegation with Inheritance** — only when the subtype relationship is genuine and stable.

## 7. Big Refactorings

Multi-session work. Requires an explicit plan, a safety net, and staged milestones — Heavy tier in SKILL.md §1.

- **Tease Apart Inheritance** — one hierarchy mixes multiple variation axes.
- **Convert Procedural Design to Objects** — data and behavior need clearer ownership.
- **Separate Domain from Presentation** — UI and policy are tangled.
- **Extract Hierarchy** — several types share behavior with meaningful variation.
