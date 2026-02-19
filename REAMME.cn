# promptica

> **专为 Claude Skills 编写而生的简洁、精炼、无歧义 DSL。**

**promptica** 是一种领域专用语言（DSL），专为编写结构化、可靠且可扩展的 [Claude Skills](https://docs.claude.com) 而设计 —— 那些教会 Claude 执行特定工作流程、领域知识与可重复任务的指令集。

它填补了自由格式 Markdown 指令与工程级 Prompt 逻辑之间的空白，将控制流程、变量、函数与错误处理引入 Skill 的编写实践。

---

## 为什么选择 Promptica？

Claude Skills 通常以纯 Markdown 编写——灵活，但脆弱。随着 Skill 复杂度提升，自由格式的 Markdown 难以清晰表达：

- **条件行为** —— 根据上下文或用户输入给出不同响应
- **迭代工作流程** —— 重试逻辑、多步骤序列、循环
- **可复用逻辑** —— 跨多个 Skill 共享的函数
- **健壮的错误处理** —— 发生异常时的优雅降级机制

传统编程语言虽能表达上述一切，却对 LLM 原生指令设计而言过于僵硬冗长。纯粹的 Prompt 链式调用则难以调试、不易维护，且无法规模化。

**promptica** 提供务实的中间地带：一种精炼、富有表达力的 DSL，专为 Claude Skill 编写的结构与意图而设计。

---

## 设计理念

**promptica** 的每一个语法决策都经过深思熟虑。以下说明各项核心设计选择的理由。

### `$` 变量前缀

所有变量均以 `$` 为前缀：

```promptica
SET $difficulty = "hard";
SET $attempts = 0;
```

`$` 前缀是刻意的设计选择，而非单纯的惯例。它作为**通用视觉锚点** —— 每当看到 `$`，你立即知道这是一个运行时变量，而非关键字、字符串字面量或控制流程构件。在复杂的嵌套逻辑中，当变量与关键字并列出现时，这一区别至关重要：

```promptica
IF ($attempts > $max_retries) {
  RESPOND "已达最大重试次数。";
}
```

若没有 `$`，`attempts` 与 `max_retries` 一眼看去与关键字毫无区别。有了 `$`，无论对人类读者还是 LLM 而言，意图都清晰无误。这一惯例在 PHP、Bash、Perl 等语言以及 Handlebars、Twig 等模板引擎中广泛使用，对大多数开发者而言一看即懂。

---

### 符号运算符：`==`、`!=`、`>`、`<`

**promptica** 采用标准符号比较运算符，而非英文单词替代方案：

```promptica
IF ($score >= 80 AND $attempts != 0) {
  RESPOND "表现良好。";
}
```

这是刻意的 **Token 效率**决策。符号运算符各自只占单个 Token。`IS NOT`、`EXCEEDS`、`BELOW` 等英文替代词消耗更多 Token，引入歧义，且不具备实质的理解优势 —— LLM 经过数十亿行代码的训练，解析 `==`、`!=`、`>` 与 `<` 的能力与解析自然语言无异。保留符号运算符在缩减 Prompt 体积的同时，完整保留了清晰度。

---

### `{ }` 块分隔符

所有多语句块均使用显式大括号：

```promptica
IF ($difficulty == "hard") {
  RESPOND "这是一个复杂的挑战。";
  SUGGEST "将问题拆解为更小的步骤。";
}
```

缩进式作用域（如 Python）在 Prompt 环境中十分脆弱。空白字符与换行符常在以下情况被截断或折叠：复制粘贴操作、API 请求规范化、Markdown 渲染、编辑器自动格式化。大括号使块边界**显式且结构稳定**，无论 Skill 以何种方式传输或存储皆然。这是一项以可靠性为优先的决策。

---

### `IF / ELIF / ELSE` 条件语法

**promptica** 采用 `ELIF` 进行链式条件判断，与 Python 保持一致：

```promptica
IF ($score > 80) {
  RESPOND "优秀。";
} ELIF ($score > 60) {
  RESPOND "表现不错。";
} ELSE {
  RESPOND "继续努力。";
}
```

`ELIF` 优于 `ELSE IF`，因为它是单一关键字 —— 更紧凑、视觉噪音更少，也不易被误读为两个独立构件。它对大多数开发者而言耳熟能详，有效降低了学习成本。

---

### 大写关键字

所有保留关键字 —— `SET`、`IF`、`ELIF`、`ELSE`、`WHILE`、`FOREACH`、`MATCH`、`CASE`、`RESPOND`、`ASK`、`SUGGEST`、`FUNCTION`、`RETURN`、`TRY`、`CATCH`、`FINALLY` 等 —— 均以大写书写：

```promptica
FOREACH ($topic IN $topics) {
  RESPOND "现在讲解 $topic。";
}
```

大写关键字建立即时的视觉层次。控制流程与输出指令从变量值和字符串内容中清晰突显，即便在冗长、深度嵌套的工作流程中，也能一眼识别 Skill 的结构骨架。

---

### 分号语句终止符

所有语句以分号结尾：

```promptica
SET $name = "Alice";
RESPOND "你好，$name！";
```

分号使语句边界显式且无歧义。在换行符可能无法稳定保留的 Prompt 环境中，分号作为不依赖空白字符的可靠终止符。这与 `{ }` 块的设计理念一脉相承 —— 结构应显式编码，而非从格式推断。

---

### `MATCH / CASE` 模式匹配

对于多分支意图路由，**promptica** 提供 `MATCH / CASE` 作为深度嵌套 `IF / ELIF` 链的简洁替代方案：

```promptica
MATCH ($user_intent) {
  CASE "code_generation" {
    RESPOND "我来为你编写代码。";
  }
  CASE "debugging" {
    RESPOND "让我协助排查这个问题。";
  }
  DEFAULT {
    ASK "能说明一下你需要什么吗？";
  }
}
```

`->` 箭头刻意省略 —— `{ }` 块分隔符本身已足以标示每个 case 主体的起始位置，在不牺牲清晰度的前提下保持语法精炼。此构件自然对应真实世界的 Claude Skill 路由场景，即 Skill 需要识别用户意图并分发至对应处理逻辑的情境。

---

### 一等公民的错误处理

**promptica** 通过 `TRY / CATCH / FINALLY` 将错误处理视为一等公民：

```promptica
TRY {
  STEP "尝试执行操作";
} CATCH ("timeout_error") {
  RESPOND "操作超时，以下提供简化的回答。";
} FINALLY {
  RESPOND "还有其他我可以帮助的地方吗？";
}
```

Claude Skill 工作流程时常遭遇难以预测的情况 —— 超时、模糊输入、缺少上下文。将这些情况视为可捕获、可处理的异常，而非无声的失败，能使 Skills 在生产环境中显著更加健壮。

---

## 核心价值

**promptica** 让 Skill 开发者得以：

- 清晰表达**条件行为**，无需将逻辑埋藏在自然语言描述中
- 通过函数与模块构建**可复用、可组合**的 Skill 逻辑
- 提升 Claude Skills 的**可读性、可维护性与可靠性**
- 从简单的单一任务 Skill 扩展至复杂的多步骤工作流程，无需全面重写

最终带来更快的迭代速度、更清晰的意图表达，以及更健壮的 Claude 驱动体验。

---

## 创作动机

在反复试验多种现有 DSL 与 Prompt 编排方案后，我通过真实的 Claude Skill 编写实践持续打磨自己的方法论。现有解决方案大多不是过于接近传统编程语言，就是对生产环境而言结构过于松散。

**promptica** 是这些实践心得的提炼结晶 —— 为所有投入 Claude Skills 开发的人，设计得简洁、精炼、无歧义。

---

## 许可证

依据 **Apache License, Version 2.0（Apache-2.0）** 授权发布。
