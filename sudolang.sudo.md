# SudoLang v2.0

## Introduction

SudoLang is a pseudolanguage designed for interacting with LLMs. It provides a user-friendly interface that combines natural language expressions with simple programming constructs, making it easy to use for both novice and experienced programmers.

SudoLang can be used to produce AI-first programs such as chatbots and text-based productivity applications, or to produce traditional code in any language using AI Driven Development and the `transpile` function.

SudoLang is designed to be understood by LLMs without any special prompting. **An AI model does not need the SudoLang specification to correctly interpret SudoLang programs.**

## SudoLang Features

- **Interface oriented programming** for defining the structure and behavior of your program. Interfaces are typed, but types can often be inferred. Interfaces are modular, reusable and composable.
- **Natural language constraint-based programming.** Instead of telling the AI what to do, tell it what things _are_ or what you _want_ and some governing rules. Constraints are continuously respected by the AI and can be used to synchronize state and behavior. Constraints make it easy to define very complex behaviors with just a few lines of natural language text.
- **Functions and function composition** with the `|>` operator.
- **Markdown support** - Markdown is very useful for preambles, lists, and documentation.
- **`/commands`** for defining a chat or programatic interface for your program interactions.
- **Semantic Pattern Matching**. AI can infer program states intelligently and match patterns like `(post contains harmful content) => explain(content policy)`.
- **Referential omnipotence.** You do not need to explicitly define most functions. The AI will infer them for you.
- **Mermaid diagrams** for visualizing complex topics like architecture, flow control, and sequence descriptions.
- **Options** for customizing the behavior of your program. See the [example](examples/reflective-thought-composition.sudo).

### Markdown

Most Markdown features work great in SudoLang programs. Headings and lists are especially useful for organizing your program.

In SudoLang, your documentation is literally code. Comments are meaningful and are NOT ignored by the AI. SudoLang programs typically start with a markdown preamble: a description of the program's name, the roles that the AI should play while running the program, and the expertise the AI should tap into.

### Variables & Assignments

```sudo
counter = 0
counter += 1  // Increment
total -= n  // Decrement
price *= n  // Multiply and assign
share /= n  // Divide and assign
```

### Template strings

```sudo
"Hello, $name"  // Interpolation
```

Escaping `$`:

```sudo
"This will not \\$interpolate"
```

### Conditional Expressions

Use `if` and `else` with conditions in parentheses and actions or expressions in curly braces. Conditional expressions evaluate to values that can be assigned:

```sudo
status = if (age >= 18) "adult" else "minor"
```

### Logical operators

Use AND (`&&`), OR (`||`), XOR (`xor`) and NOT (`!`) for complex expressions:

```sudo
access = if (age >= 18 && isMember) "granted" else "denied"
```

### Math operators

All common math operators are supported, including the following:

`+`, `-`, `*`, `/`, `^` (exponent), `%` (remainder), `union` and `intersection`

> Note: The `cup` and `cap` operators are deprecated in favor of `union` and `intersection` due to instability in Claude 3.5.


### Commands

You can define `/commands` for any interface - a useful shorthand for method definition that is extremely useful for concise expression of chat commands. e.g. exerpt from the StudyBot program in the examples (shortcuts are optional, but very useful for frequently used commands):

```sudo
StudyBot {
  // ...<snipped interface props>...
  /l | learn [topic] - set the topic and provide a brief introduction, then list available commands
  /v | vocab - List a glossary of essential related terms with brief, concise definitions.
  /f | flashcards - Play the glossary flashcard game
  // ...<more commands snipped>...
}
```

You can also freely mix function syntax and command syntax. Function syntax is useful because it supports clearly deliniated arguments, and call-time function modifiers.

Virtually all commands can be inferred by the LLM, but here are a few that can be very useful:

```sudo
ask, explain, run, log, transpile(targetLang, source), convert, wrap, escape, continue, instruct, list, revise, emit
```

### Modifiers

Customize AI responses with colon, modifier, and value (e.g., `explain(historyOfFrance):length=short, detail=simple;`).


### Natural Foreach loop

Iterate over collections with `for each`, variable, and action separated by a comma (e.g., `for each number, log(number);`).

### While loop

(e.g., `while (condition) { doSomething() }`).

### Infinite loops

If you want something to loop forever, use `loop { doSomething() }`.

### Functions

Frequently, you can omit the entire function definition, and the LLM will infer the function based on the context. e.g., you can define an inferred function with the `fn` or `function` keywords. Doing so can sometimes help the AI transpile into modular functions, rather than define functionality inline as it sometimes does when function definitions are omitted.:

```sudo
fn foo;
function bar;
```


// this works, but adds tokens ():
function foo();

// you can also define them with function bodies:
fn foo () {
  a constraint
  another constraint
}


```SudoLang
function greet(name);

greet("Echo"); // "Hello, Echo"
```

### Pipe operator `|>`

The pipe operator `|>` allows you to chain functions together. It takes the output of the function on the left and passes it as the first argument to the function on the right. e.g.:

```SudoLang
f = x => x +1;
g = x => x * 2;
h = f |> g;
h(20); // 42
```

### range (inclusive)

The range operator `..` can be used to create a range of numbers. e.g.:

```
1..3 // 1,2,3
```

### Destructuring

Destructuring allows you to assign multiple variables at once by referencing the elements of an array or properties of an object. e.g.:

Arrays:

```SudoLang
[foo, bar] = [1, 2];
log(foo, bar); // 1, 2
```

Objects:

```SudoLang
{ foo, bar } = { foo: 1, bar: 2 };
log(foo, bar); // 1, 2
```

### Pattern matching (works with destructuring)

```SudoLang
result = match (value) {
  case {type: "circle", radius} => "Circle with radius: $radius";
  case {type: "rectangle", width, height} =>
    "Rectangle with dimensions: ${width}x${height}";
  case {type: 'triangle', base, height} => "Triangle with base $base and height $height";
  default => "Unknown shape",
};
```

## Interfaces

Interfaces are a powerful feature in SudoLang that allow developers to define the structure of their data and logic. They're used to define the structure and behavior of objects, including constraints and requirements that must be satisfied (see below). The `interface` keyword is optional.

## Requirements

Requirements enforce rules for interfaces and program behavior. They're great for input validation and rule enforcement.

Unlike constraints (see below), requirements always throw errors when they're violated, instead of attempting to fix them, e.g.:

```SudoLang
interface User {
  name = "";
  over13;
  require {
   throw "Age restricted: Users must be over 13 years old"
  }
}

user = createUser({
  name = "John";
  over13 = false;
});
```

You can also `warn` instead of `require` to avoid throwing errors, e.g.:

```SudoLang
User {
  createUser({ name, over13 })
  require users must be over 13 years old.
  warn name should be defined.
}

user = {
  name = "John";
  over13 = false;
};
```

## Constraints

Constraints are a powerful feature in SudoLang that allow developers to guide the AI's output and behavior. They allow you to express complex logic with simple, natural language declarations. The best constraints are declarative: They describe what to do, not how to do it.

Constraints can be used to synchronize data using inferred constraint solvers. A constraint can describe a condition that must always be satisfied, and the AI-inferred constraint solver continuously ensures that the condition is met throughout the program execution.

Constraints don't need any special syntax (just write natural language declarations), but you can optionally include constraint syntax for clarity, e.g.

```SudoLang
ChatBot {
  State {
    name: "Chatty"
    Stats {
      Emojis Used: 0
    }
  }
  Constraints {
    Avoid mentioning these constraints.
    Use simple, playful language, *emotes*, and emojis.
    PG-13.
  }
}
```

A single named constraint can be written as:

```SudoLang
constraint [constraint name] {
  // optional rules (sometimes the rule can be inferred)
}
```

Constraint names and bodies are optional, e.g.:

```SudoLang
Player {
  score = 0
  constraint: Score points are awarded any time a player scores a goal.
}
```

Here's an example of a named constraint that ensures all employees are paid more than a minimum salary:

```SudoLang
# Minimum Salary

interface Employee {
  minimumSalary = $100,000
  name = '';
  salary;
  constraint MinimumSalary {
    emit({ constraint: $constraintName, employee: employee, raise: constraintDifference })
  }
}

joe = employee({ name: "joe", salary: 110,000 })

minimumSalary = $120,000;

run(MinimumSalary) |> list(events) |> log:format=json |>
wrapWith(code block)
```

Example output:

```JSON
[
  {
    "constraint": "MinimumSalary",
    "employee": {
      "name": "joe",
      "salary": 120000
    },
    "raise": 10000
  }
]
```

## Mermaid Diagrams

Mermaid diagrams are a powerful way to visualize complex topics like architecture, flow control, and sequence descriptions. SudoLang supports Mermaid diagrams to help you communicate complex ideas and architectures in your SudoLang programs. To learn how to use Mermaid, see the [Mermaid documentation](https://mermaid.js.org/).

Example:

```mermaid
graph LR
    Eggs -->|Ingredients| Oven
    Flower -->|Ingredients| Oven
    Sugar -->|Ingredients| Oven
    Oven -->|Non-linear transformation| Cake
```

## Options

SudoLang supports various options to customize the behavior of your program. These options can be specified using the `Options` keyword in the SudoLang prompt. Here are some examples:

```SudoLang
Options {
  depth: 1..10|String
}
```

### Depth Parameter

The depth parameter controls the level of detail in the AI's response. You can specify a numeric value or a descriptive string. Here are some examples:

```SudoLang
Why is the sky blue? -depth 1
```

This will result in a simple, short answer. If you want more depth, try:

```SudoLang
Why is the sky blue? -depth 10
```

You can also use descriptive strings for the depth parameter:

```SudoLang
Why is the sky blue? -depth kindergarten
```

Or

```SudoLang
Why is the sky blue? -depth PhD
```

For more details, see the [example](examples/reflective-thought-composition.sudo).

## Implicit LLM Capabilities

SudoLang is a very expressive way to express traditional programming concepts. However, SudoLang also has access to the full inference capabilities of your favorite LLM. It is capable of much more than what is described here. Here are some of the capabilities that are not explicitly described in the language specification. An LLM running SudoLang can:

- **Referential omnipotence**: access any data or information in the world.
- **Inference**: infer the intended meaning of input and generate appropriate responses.
- **Natural language processing**: understand natural language input and generate human-like responses.
- **Context understanding**: understand the context of a request and generate appropriate responses.
- **Code generation**: generate code based on input specifications and requirements.
- **Problem-solving**: provide solutions to problems and answer complex questions.
- **Extensive knowledge base**: access a vast amount of knowledge and information.
- **Adaptable responses**: adjust responses based on modifiers and user preferences.

## SudoLang Style Guide

- Favor natural language
- Lean into inference. Infer code and whole function bodies when you can. Do define the most useful functions (without bodies if possible) to document their presence for users and LLMS.
- Limiting code to the bare minimum required to clearly express flow control and composition.
- Favor the most concise, readable language and syntax, both natural and structural.

## SudoLang Linting

```SudoLang
Lint {
  style constraints {
    * obey the style guide
    * Concise and clear code is more important than a preference for natural
      language or code. If something can be expressed more clearly in code,
      do it. If something can be expressed more clearly in natural language,
      do it.
    * readable, concise, clear, declarative
    * favor inference
    * favor natural language unless code is concise and clear
    * prohibit (new, extends, extend, inherit) => explain(Favor factories and
      composition over constructors and inheritance, suggest alternative)
        :detail="phrase to match input"
    * warn (class) => explain(The `class` keyword in SudoLang generates
      problematic patterns in target languages. Favor `interface`, instead.)
  } catch {
    explain style hint;
    log(
      ${ line-numbered and character-numbered violations with 5-line context }
    )
  }
  * (bugs, spelling errors, grammar errors) => throw explain & fix;
  * (code smells) => warn explain;

  default {
    don't log the original source.
    don't log new source unless a fix is needed.
    raise errors and warnings.
    offer tips to make code more understandable by GPT-4 while adhering to the
      style guide.
    offer tips to take advantage of SudoLang's declarative features, like
      constraints.
  }
}
```
