# Promptica: A Clean, Minimal, and Unambiguous tool for LLM Skills.

## 1. Introduction

**Promptica** adopts the methodology of Domain-Specific Languages (DSL), a tool to create structured, logical, and repeatable prompts for LLMs, especially Skills, enabling developers to build complex workflows featuring control flow, variables, functions, and error handling.

### 1.1 Project Information

- **License**: Apache-2.0
- **Version**: v0.3
- **Contact**: aaron.kb.h@gmail.com
- **WeChat**: NanQuan2023

---

## 2. Core Concepts

### 2.1 Program Structure

Every Promptica program is enclosed within a `DSL` block:

```promptica
DSL {
  // Your prompt logic here
}
```

The `DSL` declaration is optional but recommended for complex scenarios, clarity and tooling support.

### 2.2 Statements and Syntax Rules

- **Statement Termination**: All statements end with a semicolon (`;`)
- **Comments**: Single-line comments start with `//`
- **Case Sensitivity**: Keywords are UPPERCASE, variables use lowercase or camelCase
- **String Literals**: Enclosed in double quotes (`"..."`)

---

## 3. Variables and Data Types

### 3.1 Variable Declaration

Variables are declared using the `SET` keyword and prefixed with `$`:

```promptica
SET $variable_name = "value";
SET $count = 0;
SET $is_valid = true;
```

### 3.2 Dictionaries

Dictionaries store key-value pairs using curly braces:

```promptica
SET $config = {
  expertise: "programming, data science";
  tone: "helpful, educational";
  max_attempts: 3;
  features: ["code generation", "debugging", "explanation"];
};
```

**Accessing Dictionary Properties**:
```promptica
SET $max_tries = $config.max_attempts;
SET $tone_style = $config.tone;
```

### 3.3 Lists

Lists store ordered collections using square brackets:

```promptica
SET $options = [
  "Option 1: Code review",
  "Option 2: Bug fixing",
  "Option 3: Architecture design"
];

SET $tasks = [
  "Analyze requirements",
  "Design solution",
  "Implement code"
];
```

### 3.4 Variable Interpolation

Variables can be embedded in strings using the `$variable_name` syntax:

```promptica
SET $name = "Alice";
RESPOND "Hello, $name! Welcome to the system.";
```

---

## 4. Control Flow Structures

### 4.1 Sequential Steps

Use `STEP` to define logical phases in your prompt:

```promptica
STEP "Phase 1: Understand user requirements";
RESPOND "Let me understand what you need.";

STEP "Phase 2: Generate solution";
RESPOND "Based on your input, here's my recommendation...";
```

### 4.2 Conditional Statements

**Basic IF-ELSE**:
```promptica
IF ($difficulty == "easy") {
  RESPOND "This is a straightforward problem.";
} ELIF ($difficulty == "medium") {
  RESPOND "This requires some careful thought.";
} ELSE {
  RESPOND "This is a complex challenge.";
}
```

**Multiple Conditions**:
```promptica
IF ($score > 80 AND $attempts < 3) {
  RESPOND "Excellent performance!";
} ELSE IF ($score > 60 OR $has_bonus == true) {
  RESPOND "Good job!";
} ELSE {
  RESPOND "Keep practicing!";
}
```

### 4.3 Loops

**WHILE Loop**:
```promptica
SET $attempts = 0;
WHILE ($attempts < 3 AND NOT $success) {
  ASK "Please provide more details.";
  SET $attempts = $attempts + 1;
  
  IF ($attempts >= 3) {
    RESPOND "I'll proceed with the information available.";
    BREAK;
  }
}
```

**DO-WHILE Loop** (executes at least once):
```promptica
DO {
  ASK "Would you like to continue?";
  // Process response
} WHILE ($user_wants_more == true);
```

**FOREACH Loop**:
```promptica
SET $topics = ["variables", "functions", "loops"];

FOREACH ($topic IN $topics) {
  RESPOND "Let me explain $topic in detail...";
  STEP "Provide explanation for $topic";
}
```

### 4.4 Pattern Matching

The `MATCH` statement provides elegant pattern matching:

```promptica
MATCH ($user_intent) {
  CASE "code_generation" {
    STEP "Generate code solution";
    RESPOND "I'll write the code for you.";
  }
  CASE "debugging" {
    STEP "Analyze and fix errors";
    RESPOND "Let me help debug that issue.";
  }
  CASE "explanation" {
    STEP "Provide detailed explanation";
    RESPOND "I'll explain this concept step by step.";
  }
  DEFAULT {
    ASK "Could you clarify what you need help with?";
  }
}
```

---

## 5. Functions

### 5.1 Function Declaration

```promptica
FUNCTION CalculateScore($correct, $total) {
  SET $percentage = ($correct / $total) * 100;
  
  IF ($percentage >= 90) {
    RETURN "excellent";
  } ELSE IF ($percentage >= 70) {
    RETURN "good";
  } ELSE {
    RETURN "needs improvement";
  }
}
```

### 5.2 Function Invocation

```promptica
// Call function and capture return value
SET $result = CALL CalculateScore(18, 20);
RESPOND "Your performance is $result.";

// Call function without capturing return value
CALL LogActivity("User completed quiz");
```

---

## 6. Output Operations

**Output Commands**:

- **RESPOND**: Generate a response to the user
- **ASK**: Pose a question to the user
- **SUGGEST**: Offer a suggestion or recommendation

```promptica
RESPOND "I understand your question.";
ASK "What programming language are you using?";
SUGGEST "Consider using a try-catch block for error handling.";
```

---

## 7. Error Handling

Use `TRY-CATCH-FINALLY` for robust error handling:

```promptica
TRY {
  STEP "Attempt to process user input";
  // Processing logic
  
  IF ($timeout_occurred) {
    THROW "timeout_error";
  }
} CATCH ("timeout_error") {
  RESPOND "The operation took too long. Let me provide a simplified answer.";
} CATCH ("invalid_input") {
  ASK "Could you rephrase your question?";
} FINALLY {
  STEP "Cleanup and finalization";
  RESPOND "Is there anything else I can help with?";
}
```

---

## 8. Operators Reference

### 8.1 Comparison Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `==` | Equal to | `$x == 5` |
| `!=` | Not equal to | `$x != 0` |
| `>` | Greater than | `$score > 80` |
| `<` | Less than | `$attempts < 3` |
| `>=` | Greater or equal | `$age >= 18` |
| `<=` | Less or equal | `$count <= 10` |

### 8.2 Logical Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `AND` | Logical AND | `$x > 0 AND $x < 10` |
| `OR` | Logical OR | `$status == "ready" OR $status == "pending"` |
| `NOT` | Logical NOT | `NOT $is_complete` |

### 8.3 String Operations

```promptica
// Check if string contains substring
IF ($text.CONTAINS("error")) {
  RESPOND "An error was detected.";
}

// Alternative syntax
IF ($text CONTAINS "warning") {
  RESPOND "Warning found.";
}

// Regular expression matching
IF (REGEX($email, "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$")) {
  RESPOND "Valid email format.";
}

// String metrics
SET $word_count = COUNT_WORDS($text);
SET $char_count = COUNT_CHARS($text);
```

### 8.4 Arithmetic Operations

```promptica
SET $sum = $a + $b;
SET $difference = $a - $b;
SET $product = $a * $b;
SET $quotient = $a / $b;
SET $remainder = $a % $b;
```

---

## 9. Best Practices

### 9.1 Modularity

Break complex prompts into reusable functions:

```promptica
FUNCTION ValidateInput($input) {
  IF (NOT $input OR $input == "") {
    RETURN false;
  }
  RETURN true;
}

FUNCTION ProcessUserQuery($query) {
  IF (NOT CALL ValidateInput($query)) {
    ASK "Please provide a valid query.";
    RETURN;
  }
  
  // Process valid query
  STEP "Analyze query intent";
  // ...
}
```

### 9.2 Error Resilience

Always include fallback logic:

```promptica
SET $retry_count = 0;
SET $max_retries = 3;

WHILE ($retry_count < $max_retries) {
  TRY {
    // Attempt operation
    BREAK;  // Success - exit loop
  } CATCH ("error") {
    SET $retry_count = $retry_count + 1;
    IF ($retry_count >= $max_retries) {
      RESPOND "Unable to complete the operation. Here's what I can offer instead...";
    }
  }
}
```

### 9.3 Clear Variable Naming

Use descriptive names that convey purpose:

```promptica
// Good
SET $user_authentication_status = "verified";
SET $maximum_retry_attempts = 3;

// Avoid
SET $status = "verified";
SET $max = 3;
```

### 9.4 Structured Workflow

Organize prompts with clear phases:

```promptica
DSL {
  // 1. Initialization
  STEP "Initialize configuration";
  SET $config = { /* ... */ };
  
  // 2. Input validation
  STEP "Validate user input";
  // ...
  
  // 3. Main processing
  STEP "Process user request";
  // ...
  
  // 4. Generate output
  STEP "Generate response";
  // ...
  
  // 5. Cleanup
  STEP "Finalize and offer follow-up";
  // ...
}
```

---

## 10. Complete Example: Technical Assistant

```promptica
DSL {
  // Configuration
  SET $config = {
    max_clarification_attempts: 3;
    supported_languages: ["python", "javascript", "java", "go"];
    complexity_levels: ["beginner", "intermediate", "advanced"];
  };
  
  // Helper function: Calculate relevance score
  FUNCTION CalculateRelevance($query, $keywords) {
    SET $score = 0;
    
    FOREACH ($keyword IN $keywords) {
      IF ($query.CONTAINS($keyword)) {
        SET $score = $score + 1;
      }
    }
    
    RETURN $score;
  }
  
  // Helper function: Generate explanation
  FUNCTION GenerateExplanation($language, $complexity) {
    MATCH ($language) {
      CASE "python" {
        IF ($complexity == "beginner") {
          RESPOND "For Python beginners, start with basic functions and control flow.";
          RESPOND "Example: Use def to define functions, and remember Python uses indentation.";
        } ELSE {
          RESPOND "For advanced Python, focus on async/await, type hints, and error handling.";
          RESPOND "Consider using context managers and decorators for cleaner code.";
        }
      }
      CASE "javascript" {
        RESPOND "For JavaScript, master promises, async/await, and modern ES6+ features.";
        RESPOND "Focus on understanding closures, arrow functions, and the event loop.";
      }
      DEFAULT {
        RESPOND "I can provide guidance for: $config.supported_languages";
      }
    }
  }
  
  // Main workflow
  STEP "1. Greet and understand user needs";
  RESPOND "Hello! I'm your technical coding assistant. How can I help you today?";
  
  // Input clarification loop
  SET $clarification_attempts = 0;
  SET $query_is_clear = false;
  
  WHILE (NOT $query_is_clear AND $clarification_attempts < $config.max_clarification_attempts) {
    IF (COUNT_WORDS($user_query) < 5) {
      ASK "Could you provide more details about what you're trying to achieve?";
      SET $clarification_attempts = $clarification_attempts + 1;
    } ELSE {
      SET $query_is_clear = true;
    }
    
    IF ($clarification_attempts >= $config.max_clarification_attempts) {
      RESPOND "I'll help based on what I understand so far.";
      BREAK;
    }
  }
  
  // Analyze query intent
  STEP "2. Analyze query and determine assistance type";
  
  SET $programming_keywords = ["code", "function", "debug", "error", "implement"];
  SET $relevance_score = CALL CalculateRelevance($user_query, $programming_keywords);
  
  IF ($relevance_score >= 2) {
    STEP "3. Provide programming assistance";
    
    // Determine language
    SET $detected_language = "unknown";
    FOREACH ($lang IN $config.supported_languages) {
      IF ($user_query.CONTAINS($lang)) {
        SET $detected_language = $lang;
        BREAK;
      }
    }
    
    IF ($detected_language != "unknown") {
      RESPOND "I see you're working with $detected_language.";
      
      // Determine complexity
      SET $complexity = "intermediate";
      IF ($user_query.CONTAINS("beginner") OR $user_query.CONTAINS("simple")) {
        SET $complexity = "beginner";
      } ELSE IF ($user_query.CONTAINS("advanced") OR $user_query.CONTAINS("complex")) {
        SET $complexity = "advanced";
      }
      
      CALL GenerateExplanation($detected_language, $complexity);
      
      SUGGEST "Consider breaking down complex problems into smaller, testable functions.";
    } ELSE {
      ASK "Which programming language are you using?";
    }
  } ELSE {
    STEP "3. Provide general technical guidance";
    RESPOND "I can help you with various technical topics:";
    
    SET $assistance_options = [
      "Code review and best practices",
      "Algorithm design and optimization",
      "Debugging and troubleshooting",
      "Architecture and design patterns"
    ];
    
    FOREACH ($option IN $assistance_options) {
      RESPOND "- $option";
    }
    
    ASK "Which area would be most helpful for you?";
  }
  
  // Error handling and wrap-up
  TRY {
    STEP "4. Provide follow-up assistance";
    RESPOND "Would you like me to elaborate on any part of the solution?";
  } CATCH ("timeout") {
    RESPOND "We're approaching the time limit. Let me summarize the key points:";
    STEP "Provide concise summary";
  } FINALLY {
    RESPOND "Feel free to ask if you need clarification or have additional questions!";
  }
}
```

---

## 11. Implementation Guidelines

### 11.1 Incremental Development

1. Start with a basic prompt structure
2. Add variables and configuration
3. Implement control flow
4. Extract reusable logic into functions
5. Add error handling
6. Test with various inputs
7. Refine and optimize

### 11.2 Testing Strategy

- Test with minimal inputs
- Test with edge cases (empty strings, zero values)
- Test with invalid inputs
- Test loop termination conditions
- Verify all code paths are reachable

### 11.3 Documentation

Include comments for:
- Complex logic
- Non-obvious design decisions
- Function parameters and return values
- Configuration options

---

## 12. Future Enhancements (Roadmap)

Potential features for future versions:
- Import/export of function libraries
- Macro system for code generation
- Type system for variables
- Debugging and profiling tools
- Integration with popular LLM APIs
- Visual prompt flow designer

---

## Appendix: Syntax Quick Reference

```promptica
// Variables
SET $var = value;

// Control Flow
IF (condition) { }
WHILE (condition) { }
FOREACH ($item IN $list) { }
MATCH ($expr) { CASE value { ... } }

// Functions
FUNCTION Name($param) { RETURN value; }
CALL Name($arg);

// Output
RESPOND "text";
ASK "question?";
SUGGEST "recommendation";

// Error Handling
TRY { } CATCH (error) { } FINALLY { }

// Operators
AND, OR, NOT
==, !=, >, <, >=, <=
CONTAINS, REGEX()
```
