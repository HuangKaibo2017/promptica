# promptica

> **A Clean, Minimal, and Unambiguous DSL for Authoring Claude Skills.**

**promptica** is a Domain-Specific Language (DSL) purpose-built for writing structured, reliable, and scalable [Claude Skills](https://docs.claude.com) — the reusable instruction sets that teach Claude specific workflows, domain knowledge, and repeatable tasks.

It bridges the gap between freeform Markdown instructions and engineering-grade prompt logic, bringing control flow, variables, functions, and error handling to Skill authoring.

---

## Why Promptica?

Claude Skills are typically authored in plain Markdown — flexible, but fragile. As Skills grow in complexity, freeform Markdown struggles to express:

- **Conditional behavior** — respond differently based on context or user input
- **Iterative workflows** — retry logic, multi-step sequences, loops
- **Reusable logic** — shared functions across multiple Skills
- **Robust error handling** — graceful fallbacks when things go wrong

Traditional programming languages can express all of this, but they are too rigid and verbose for LLM-native instruction design. Pure prompt chaining, on the other hand, is hard to debug, difficult to maintain, and does not scale.

**promptica** provides a pragmatic middle ground: a minimal, expressive DSL designed specifically for the structure and intent of Claude Skill authoring.

---

## Design Considerations

Every syntax decision in **promptica** was made deliberately. Here is the rationale behind each core design choice.

### `$` Variable Prefix

All variables are prefixed with `$`:

```promptica
SET $difficulty = "hard";
SET $attempts = 0;
```

The `$` prefix is an intentional design choice, not merely convention. It acts as a **universal visual anchor** — whenever you see `$`, you immediately know you are looking at a runtime variable, not a keyword, a string literal, or a control flow construct. This distinction becomes critical in complex nested logic where variables and keywords appear side by side:

```promptica
IF ($attempts > $max_retries) {
  RESPOND "Maximum attempts reached.";
}
```

Without `$`, `attempts` and `max_retries` are visually indistinguishable from keywords at a glance. With `$`, the intent is unambiguous for both human readers and LLMs. This convention is widely established in languages like PHP, Bash, and Perl, and in template engines like Handlebars and Twig — making it immediately familiar to most developers.

---

### Symbolic Operators: `==`, `!=`, `>`, `<`

**promptica** uses standard symbolic comparison operators rather than English-word alternatives:

```promptica
IF ($score >= 80 AND $attempts != 0) {
  RESPOND "Good performance.";
}
```

This is a deliberate **token efficiency** decision. Symbolic operators are single tokens each. English alternatives like `IS NOT`, `EXCEEDS`, or `BELOW` cost more tokens, introduce ambiguity, and provide no real comprehension advantage — LLMs are trained on billions of lines of code and parse `==`, `!=`, `>`, and `<` as natively as any natural language construct. Keeping symbolic operators reduces prompt size while preserving full clarity.

---

### `{ }` Block Delimiters

All multi-statement blocks use explicit curly braces:

```promptica
IF ($difficulty == "hard") {
  RESPOND "This is a complex challenge.";
  SUGGEST "Break the problem into smaller steps.";
}
```

Indentation-based scoping (as in Python) is fragile in prompt environments. Whitespace and line breaks are routinely stripped or collapsed by copy-paste operations, API request normalization, Markdown rendering, and editor auto-formatting. Curly braces make block boundaries **explicit and structure-preserving** regardless of how the Skill is transmitted or stored. This is a reliability-first decision.

---

### `IF / ELIF / ELSE` Conditional Syntax

**promptica** uses `ELIF` for chained conditionals, consistent with Python:

```promptica
IF ($score > 80) {
  RESPOND "Excellent.";
} ELIF ($score > 60) {
  RESPOND "Good job.";
} ELSE {
  RESPOND "Keep practicing.";
}
```

`ELIF` is preferred over `ELSE IF` because it is a single keyword — more compact, less visually noisy, and less likely to be misread as two separate constructs. It is familiar to a large portion of the developer community, reducing the learning curve.

---

### UPPERCASE Keywords

All reserved keywords — `SET`, `IF`, `ELIF`, `ELSE`, `WHILE`, `FOREACH`, `MATCH`, `CASE`, `RESPOND`, `ASK`, `SUGGEST`, `FUNCTION`, `RETURN`, `TRY`, `CATCH`, `FINALLY`, and others — are written in UPPERCASE:

```promptica
FOREACH ($topic IN $topics) {
  RESPOND "Explaining $topic now.";
}
```

UPPERCASE keywords create an immediate visual hierarchy. Control flow and output commands stand out clearly from variable values and string content, making it easy to identify the structural skeleton of a Skill at a glance — even in long, deeply nested workflows.

---

### Semicolon Statement Termination

All statements end with a semicolon:

```promptica
SET $name = "Alice";
RESPOND "Hello, $name!";
```

Semicolons make statement boundaries explicit and unambiguous. In prompt environments where newlines may be inconsistently preserved, the semicolon acts as a reliable terminator that does not depend on whitespace. This is consistent with the same reasoning behind `{ }` blocks — structure should be encoded explicitly, not inferred from formatting.

---

### `MATCH / CASE` Pattern Matching

For multi-branch intent routing, **promptica** provides `MATCH / CASE` as a cleaner alternative to deeply nested `IF / ELIF` chains:

```promptica
MATCH ($user_intent) {
  CASE "code_generation" {
    RESPOND "I will write the code for you.";
  }
  CASE "debugging" {
    RESPOND "Let me help debug that issue.";
  }
  DEFAULT {
    ASK "Could you clarify what you need?";
  }
}
```

The `->` arrow is intentionally omitted — the `{ }` block delimiter alone is sufficient to mark where each case body begins, keeping the syntax minimal without sacrificing clarity. This construct maps naturally to real-world Claude Skill routing scenarios, where the Skill must classify user intent and dispatch to the appropriate handler.

---

### First-Class Error Handling

**promptica** treats error handling as a first-class concern via `TRY / CATCH / FINALLY`:

```promptica
TRY {
  STEP "Attempt operation";
} CATCH ("timeout_error") {
  RESPOND "The operation timed out. Here is a simplified answer.";
} FINALLY {
  RESPOND "Is there anything else I can help with?";
}
```

Claude Skill workflows frequently encounter conditions that cannot be predicted in advance — timeouts, ambiguous inputs, missing context. Treating these as catchable, handleable events rather than silent failures makes Skills significantly more robust in production.

---

## The Value Proposition

**promptica** enables Skill authors to:

- Express **conditional behavior** clearly without burying logic in prose
- Build **reusable, composable** Skill logic through functions and modules
- Improve **readability, maintainability, and reliability** of Claude Skills
- Scale from simple single-task Skills to complex multi-step workflows without rewrites

The result is faster iteration, clearer intent, and more robust Claude-powered experiences.

---

## Motivation

After experimenting with multiple existing DSLs and prompt orchestration approaches, I continuously refined my own methodology through real-world Claude Skill authoring. Most existing solutions were either too close to traditional programming languages or too unstructured for production use.

**promptica** represents a distillation of those learnings — designed to be clean, minimal, and unambiguous for anyone building with Claude Skills.

---

## License

Released under the **Apache License, Version 2.0 (Apache-2.0)**.
