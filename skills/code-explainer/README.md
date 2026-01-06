# Code Explainer Skill

> Clear, structured explanations of code functionality and design.

## Overview

This skill helps users understand code by providing structured explanations that cover purpose, mechanics, design decisions, and trade-offs. It breaks down complex code into digestible components.

## Quick Start

When using Claude Code, simply ask about code:

```
How does this authentication middleware work?
```

```
Explain what this recursive function does
```

```
What's the purpose of this design pattern?
```

Claude will use this skill to provide comprehensive explanations.

## Features

### Structured Framework

- High-level summary
- Component breakdown
- Data flow visualization
- Design decision rationale
- Complexity analysis

### Comprehensive Coverage

- Common code patterns (async, recursion, closures)
- Language-specific concepts
- Edge case handling
- Performance characteristics

### Clear Communication

- Start simple, add depth progressively
- Use analogies for complex ideas
- Provide concrete examples
- Include visual diagrams when helpful

## Structure

```
code-explainer/
├── SKILL.md    # Main skill documentation
└── README.md   # This file
```

## Usage Examples

### JavaScript Async Code

```javascript
// Ask: "How does this work?"
async function fetchUserData(userId) {
  const response = await fetch(`/api/users/${userId}`);
  const data = await response.json();
  return data;
}
```

**Explanation covers**:
- async/await behavior
- Promise chain
- Error handling
- Event loop timing

### Python List Comprehension

```python
# Ask: "What does this do?"
squares = [x**2 for x in range(10) if x % 2 == 0]
```

**Explanation covers**:
- Comprehension syntax
- Filtering condition
- Memory efficiency
- Equivalent loop form

### Rust Ownership

```rust
// Ask: "Explain the ownership here"
fn process(s: String) -> String {
  s.to_uppercase()
}
```

**Explanation covers**:
- Ownership transfer
- Move semantics
- Lifetime implications
- Borrowing alternatives

## Explanation Template

```
## What It Does
[One-sentence summary]

## How It Works
[Step-by-step breakdown]

## Key Components
- [Component]: [Purpose]
- [Component]: [Purpose]

## Data Flow
[Input → Process → Output]

## Design Decisions
- [Decision]: [Rationale]

## Complexity
- Time: [Big O]
- Space: [Big O]
```

## Common Patterns Explained

| Pattern | Description |
|---------|-------------|
| Singleton | Single instance guarantee |
| Observer | Event-driven updates |
| Factory | Encapsulated object creation |
| Strategy | Interchangeable algorithms |
| Decorator | Behavior augmentation |

## Best Practices

### For Explanations

- Start with the "what" before the "how"
- Use analogies for abstract concepts
- Show input/output examples
- Include complexity analysis
- Mention trade-offs

### For Understanding Code

- Read code top-to-bottom
- Identify main entry point
- Trace data flow
- Look for error handling
- Check edge cases

## Language-Specific Concepts

| Language | Key Concepts |
|----------|--------------|
| JavaScript | Closures, Promises, Event Loop |
| Python | Comprehensions, Decorators, Generators |
| Rust | Ownership, Lifetimes, Traits |
| Go | Goroutines, Channels, Interfaces |
| Java | Streams, Lambdas, Optional |

## License

MIT
