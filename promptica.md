
# 1. Introduction
**Promptica** - LLMP (LLM Prompting Language) is a domain-specific language designed to create structured, logical, and repeatable prompts for Large Language Models. This document defines the complete grammar specification and provides comprehensive examples.

## 1.1 sth You should know before using it
**license**: Apache-2.0 license
**version**: v0.1
**eMail**: aaron.kb.h@gmail.com
**wx**: NanQuan2023

## 2. Grammar Specification
### 2.1 Basic Structure
[Optional] Every LLMP program begins with a DSL declaration:
```
DSL {
  // Prompt content
}
```

## 2.2 Statement
Each statement ends with a semicolon (;).
```
RESPOND "Hello, world!";
```

## 2.3 Variable
Variables are declared and assigned using the SET keyword:
```
SET $variable_name = value;
```

## 2.4 List and Dictionary
### 2.4.1 Dictionary
Dictionaries are created using curly braces:
```
SET $config = {
  expertise: "programming, data science";
  tone: "helpful, educational";
  max_attempts: 3;
};
```

Dictionary properties can be accessed using dot notation:
```
SET $max_tries = $config.max_attempts;
```
Variable references use the $variable_name syntax:
```
RESPOND "Hello, $user_name!";
```

### 2.4.2 List
```
SET @a_list = [
  "First item",
  ITEM "Second item",
];
```

## 2.5 Control Flow
### 2.5.1 Step
```
STEP "Instruction or content for this step";
```

### 2.5.2 Conditional Statement
```
IF (condition) {
  // Code executed if condition is true
} ELSE IF (another_condition) {
  // Code executed if another_condition is true
} ELSE {
  // Code executed if no conditions are true
}
```

### 2.5.3 Loop
While Loop:
```
WHILE (condition) {
  // Code executed repeatedly while condition is true

  IF (exit_condition) {
    BREAK;  // Exit the loop
  }
}
```

Do-While Loop [I did not use it in my own practice]:
```
DO {
  // Code executed at least once, then repeatedly while condition is true
} WHILE (condition);
```

Foreach Loop:
```
FOREACH (item IN $collection) {
  // Code executed for each item in collection
}
```

## 2.6 Pattern Matching
```
MATCH (expression) {
  CASE pattern1 -> {
    // Code executed if expression matches pattern1
  }
  CASE pattern2 -> {
    // Code executed if expression matches pattern2
  }
  DEFAULT -> {
    // Code executed if no patterns match
  }
}
```

2.7 Function
Function Declaration:
```
FUNCTION FunctionName($param1, $param2) {
  // Function body
  RETURN value;  // Optional return value
}
```

Function Call:
```
CALL FunctionName($param1, $param2);

// or with return value:
SET result = CALL FunctionName($param1, $param2);
```

## 2.8 Error Handling
```
TRY {
  // Code that might cause an error
} CATCH (error_condition) {
  // Code executed if error occurs
} FINALLY {
  // Code always executed after try/catch
}
```

## 2.9 Output Generation
```
RESPOND "Response text";
ASK "Question to the user?";
SUGGEST "Suggestion for the user";
```

## 2.10 Code Blocks
[I did not use it in my own practice]
```
CODE {
  language: "python";
  // Code content
}
```

# 3. Operators
## 3.1 Logical Operators
- AND - Logical AND
- OR - Logical OR
- NOT - Logical NOT
## 3.2 Comparison Operators
- == - Equal to
- != - Not equal to
- > - Greater than
- < - Less than
- >= - Greater than or equal to
- <= - Less than or equal to
## 3.3 String Operations
- $string.CONTAINS($substring) - Check if string contains substring
- $string CONTAINS $substring - Check if string contains substring
- REGEX(string, pattern, flags) - Match string against the regex pattern
- COUNT_WORDS(string) - Count words in string
- COUNT_CHARS(string) - Count characters in string

## 4. Complete Example
```
DSL {
  SET $config = {
    expertise: "programming, data science";
    tone: "helpful, educational";
    max_attempts: 3;
  };
  FUNCTION CalculateRelevance($query, $topic) {
    SET $score = 0;

    IF ($query.CONTAINS($topic)) {
      SET $score = $score + 5;
    }

    IF ($query.CONTAINS("explain") OR $query.CONTAINS("how")) {
      SET $score = $score + 3;
    }

    RETURN $score;
  }

  FUNCTION GenerateExample($concept, $difficulty) {
    IF ($difficulty == "beginner") {
      RETURN "Here's a simple example of $concept...";
    } ELSEIF ($difficulty == "intermediate") {
      RETURN "For a more advanced example of $concept...";
    } ELSE {
      RETURN "Here's an expert-level demonstration of $concept...";
    }
  }
  // Main prompt flow
  STEP "Greet the user";
  RESPOND "Hello! I'm your technical assistant. How can I help you today?";

  SET $clarity_attempts = 0;

  WHILE ("query is unclear" AND clarity_attempts < $config.max_attempts) {
    ASK "Could you please clarify what you're looking for?";
    INCREMENT $clarity_attempts;

    IF ($clarity_attempts >= $config.max_attempts) {
      RESPOND "Let me try my best with what I understand so far.";
      BREAK;
    }
  }
  // Process the query
  SET $relevance = CALL CalculateRelevance($user_query, "programming");

  IF ($relevance > 5) {
    STEP "Analyze programming question";

    MATCH ($programming_language) {
      CASE "python" -> {
        RESPOND "I see you're working with Python.";
        SET example = CALL GenerateExample("Python", user_skill_level);
        RESPOND $example;

        CODE {
          language: "python";
          """
          def example_function(param):
              # This demonstrates the concept
              result = param * 2
              return result

          # Usage example
          output = example_function(21)
          print(f"The result is: {output}")
          """
        }
      }

      CASE "javascript" -> {
        RESPOND "JavaScript is a great choice for this.";
        SET $example = CALL GenerateExample("JavaScript", user_skill_level);
        RESPOND $example;

        CODE {
          language: "javascript";
          """
          function exampleFunction(param) {
            // This demonstrates the concept
            const result = param * 2;
            return result;
          }

          // Usage example
          const output = exampleFunction(21);
          console.log(The result is: ${output});
          """
        }
      }

      DEFAULT -> {
        RESPOND "I can help with various programming languages.";
        ASK "Which programming language are you using?";
      }
    }
  } ELSE {
    STEP "Provide general assistance";
    RESPOND "I understand you need help with a technical question.";
    SET $a_list = [
      "I can explain programming concepts",
      "I can write code examples",
      "I can debug issues in your code",
    ];
    ASK "Which of these would be most helpful?";
  }

  TRY {
    STEP "Provide follow-up assistance";
    // Additional assistance logic
  } CATCH ("time limit exceeded") {
    RESPOND "We're running out of time, so let me summarize:";
    STEP "Provide quick summary";
  } FINALLY {
    RESPOND "Is there anything else I can help with?";
  }
}
```

# Design Thoughts & Suggestions
1. **Modularity**: Break complex prompts into functions for reusability
2. **Error Handling**: Always include fallback responses for unexpected inputs
3. **Clarity**: Use descriptive variable names and STEP descriptions
4. **Incremental Development**: Start with a basic prompt and expand functionality
5. **Testing**: Test prompts with different inputs to ensure robustness
