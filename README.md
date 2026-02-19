# promptica

> **A Clean, Minimal, and Unambiguous structured for LLM Skills.**

## 1. Introduction

The tools that endure are the ones that stay out of your way. They do exactly what they promise, nothing more, nothing less.

**Promptica** adopts the methodology of Domain-Specific Languages (DSL), specifically designed for crafting structured, reliable, and scalable LLM Skills—instruction sets that guide LLMs to execute specific workflows, apply domain knowledge, and perform repeatable tasks.

When Claude Skills emerged, I recognized a familiar pattern: a powerful capability, constrained by the lack of a suitable language for expression. Free-form Markdown is a starting point, not an end point. Promptica is my attempt.

It bridges the gap between freeform Markdown instructions and engineering-grade prompt logic, bringing control flow, variables, functions, and error handling to Skill authoring — without the weight of a full programming language.

### 1.1 Project Information

- **License**: Apache-2.0
- **Version**: v0.3
- **Contact**: aaron.kb.h@gmail.com
- **WeChat**: NanQuan2023

---

## 2. Why Promptica?

I have navigated enough technology cycles to recognize the moment when a new capability outgrows its scaffolding. LLM Skills are at that moment now.

Skills are typically authored in plain Markdown — and Markdown is a fine starting point. But as anyone who has built production systems knows, "flexible" and "fragile" are often the same thing. As Skills grow in complexity, freeform prose struggles to reliably express:

- **Conditional behavior** — respond differently based on context or user input
- **Iterative workflows** — retry logic, multi-step sequences, loops
- **Reusable logic** — shared functions across multiple Skills
- **Robust error handling** — graceful fallbacks when things go wrong

The instinct at this point is usually to reach for a real programming language. But that trades one problem for another — traditional code is too rigid, too verbose, and too far removed from the way LLMs actually reason. Pure prompt chaining is the other extreme: fast to write, slow to maintain, and nearly impossible to debug at scale.

After experimenting with every approach in between, I kept arriving at the same conclusion: what this space needs is not more code, and not more prose. It needs a language designed specifically for the problem. **promptica** is that language — a pragmatic middle ground built from real experience, not theory.

---

## 3. Design Considerations

Every syntax decision in **promptica** was made deliberately. Here is the rationale behind each core design choice.

### 3.1 `$` Variable Prefix

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

### 3.2 Symbolic Operators: `==`, `!=`, `>`, `<`

**promptica** uses standard symbolic comparison operators rather than English-word alternatives:

```promptica
IF ($score >= 80 AND $attempts != 0) {
  RESPOND "Good performance.";
}
```

This is a deliberate **token efficiency** decision. Symbolic operators are single tokens each. English alternatives like `IS NOT`, `EXCEEDS`, or `BELOW` cost more tokens, introduce ambiguity, and provide no real comprehension advantage — LLMs are trained on billions of lines of code and parse `==`, `!=`, `>`, and `<` as natively as any natural language construct. Keeping symbolic operators reduces prompt size while preserving full clarity.

### 3.3 `{ }` Block Delimiters

All multi-statement blocks use explicit curly braces:

```promptica
IF ($difficulty == "hard") {
  RESPOND "This is a complex challenge.";
  SUGGEST "Break the problem into smaller steps.";
}
```

Indentation-based scoping (as in Python) is fragile in prompt environments. Whitespace and line breaks are routinely stripped or collapsed by copy-paste operations, API request normalization, Markdown rendering, and editor auto-formatting. Curly braces make block boundaries **explicit and structure-preserving** regardless of how the Skill is transmitted or stored. This is a reliability-first decision.

### 3.4 `IF / ELIF / ELSE` Conditional Syntax

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

### 3.5 UPPERCASE Keywords

All reserved keywords — `SET`, `IF`, `ELIF`, `ELSE`, `WHILE`, `FOREACH`, `MATCH`, `CASE`, `RESPOND`, `ASK`, `SUGGEST`, `FUNCTION`, `RETURN`, `TRY`, `CATCH`, `FINALLY`, and others — are written in UPPERCASE:

```promptica
FOREACH ($topic IN $topics) {
  RESPOND "Explaining $topic now.";
}
```

UPPERCASE keywords create an immediate visual hierarchy. Control flow and output commands stand out clearly from variable values and string content, making it easy to identify the structural skeleton of a Skill at a glance — even in long, deeply nested workflows.

### 3.6 Semicolon Statement Termination

All statements end with a semicolon:

```promptica
SET $name = "Alice";
RESPOND "Hello, $name!";
```

Semicolons make statement boundaries explicit and unambiguous. In prompt environments where newlines may be inconsistently preserved, the semicolon acts as a reliable terminator that does not depend on whitespace. This is consistent with the same reasoning behind `{ }` blocks — structure should be encoded explicitly, not inferred from formatting.

### 3.7 `MATCH / CASE` Pattern Matching

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

The `->` arrow is intentionally omitted — the `{ }` block delimiter alone is sufficient to mark where each case body begins, keeping the syntax minimal without sacrificing clarity. This construct maps naturally to real-world LLM Skill routing scenarios, where the Skill must classify user intent and dispatch to the appropriate handler.

### 3.8 First-Class Error Handling

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

LLM Skill workflows frequently encounter conditions that cannot be predicted in advance — timeouts, ambiguous inputs, missing context. Treating these as catchable, handleable events rather than silent failures makes Skills significantly more robust in production.

---

## 4. The Value Proposition

**promptica** enables Skill authors to:

- Express **conditional behavior** clearly without burying logic in prose
- Build **reusable, composable** Skill logic through functions and modules
- Improve **readability, maintainability, and reliability** of LLM Skills
- Scale from simple single-task Skills to complex multi-step workflows without rewrites

The result is faster iteration, clearer intent, and more robust LLM-powered experiences.

---

## 5. License

Released under the **Apache License, Version 2.0 (Apache-2.0)**.
