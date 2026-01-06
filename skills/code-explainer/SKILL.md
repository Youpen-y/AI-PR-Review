---
name: code-explainer
description: Explain code functionality, patterns, and design decisions. Use when user asks "how does this work?", "what does this code do?", or requests code explanation.
---

# Code Explainer

Help users understand code functionality, patterns, and design decisions through clear, structured explanations.

## When to Use

- User asks "how does this work?" or "what does this do?"
- User needs to understand a code snippet
- User is reviewing unfamiliar code
- User wants to learn from existing code
- User types `/explain` or similar

## Explanation Framework

### 1. High-Level Summary

Start with a concise overview (1-2 sentences):

```
"This code [does X] by [method Y]. It handles [Z scenario]."
```

### 2. Break Down by Components

For complex code, explain in logical parts:

```
## Component 1: [Name]
[Purpose and how it works]

## Component 2: [Name]
[Purpose and how it works]
```

### 3. Data Flow

Show how data moves through the code:

```
Input → [Processing] → [Transformation] → Output
```

### 4. Key Patterns

Identify and explain design patterns:

| Pattern | Purpose |
|---------|---------|
| Singleton | Ensures single instance |
| Observer | Event subscription model |
| Factory | Object creation logic |

### 5. Edge Cases & Guards

Highlight defensive code:

```javascript
// Check for null to prevent runtime errors
if (user == null) return;

// Validate input before processing
if (!isValid(email)) throw new Error();
```

## Explanation Template

```
## What It Does
[One-sentence summary of purpose]

## How It Works
[Step-by-step breakdown]

## Key Components
- **Component A**: [purpose]
- **Component B**: [purpose]
- **Component C**: [purpose]

## Data Flow
[Visual or text-based flow diagram]

## Design Decisions
- **Why X?**: [rationale]
- **Why Y?**: [rationale]

## Edge Cases Handled
- [Case 1]
- [Case 2]

## Time/Space Complexity
- Time: [Big O]
- Space: [Big O]
```

## Explanation Best Practices

### DO ✓

- Start simple, then add detail
- Use analogies for complex concepts
- Show examples of input/output
- Highlight non-obvious behavior
- Mention trade-offs made
- Include line references for context

### DON'T ✗

- Over-explain obvious code
- Use jargon without definition
- Ignore error handling
- Skip complexity analysis
- Assume prior knowledge
- Miss the "why" behind decisions

## Common Code Patterns to Explain

### Async/Await

```javascript
// Explanation: This fetches data asynchronously,
// waits for the response, then processes the result.
const data = await fetch(url);
const result = await data.json();
```

### Recursion

```javascript
// Explanation: Calls itself with smaller input
// until reaching the base case, then unwinds.
function factorial(n) {
  if (n <= 1) return 1;  // Base case
  return n * factorial(n - 1);  // Recursive step
}
```

### Closures

```javascript
// Explanation: Inner function retains access to
// outer function's variables after it returns.
function createCounter() {
  let count = 0;
  return () => ++count;
}
```

### Higher-Order Functions

```javascript
// Explanation: Takes a function as argument
// and applies it to each element.
array.map(fn).filter(predicate).reduce(accumulator);
```

## Language-Specific Tips

### JavaScript

- Explain `this` context changes
- Clarify promise chains
- Describe event loop behavior
- Note prototype inheritance

### Python

- Explain list/dict comprehensions
- Describe decorator behavior
- Clarify generator vs iterator
- Note duck typing implications

### Rust

- Explain ownership rules
- Describe borrowing/lifetimes
- Clarify trait usage
- Note unsafe blocks

## Visual Aids

When helpful, use ASCII diagrams:

```
┌─────────┐    ┌──────────┐    ┌─────────┐
│ Request │───▶│ Validate │───▶│ Process │
└─────────┘    └──────────┘    └─────────┘
                                     │
                                     ▼
                               ┌─────────┐
                               │ Response│
                               └─────────┘
```

## Questions to Guide Explanation

When explaining code, consider:

1. **What** is the primary purpose?
2. **How** does it achieve the goal?
3. **Why** was this approach chosen?
4. **When** should this be used?
5. **What are** the alternatives and trade-offs?

## Example Explanation

```
## What It Does
Authenticates users using JWT tokens stored in HTTP cookies.

## How It Works
1. Extract token from request cookie
2. Verify token signature with secret key
3. Decode token to get user ID
4. Fetch user from database
5. Attach user to request object

## Key Components
- **extractToken**: Parses cookie header
- **verifyToken**: Validates JWT signature
- **getUser**: Queries database

## Data Flow
Request → Extract Token → Verify → Decode → Fetch User → Attach to Request

## Design Decisions
- **Why cookies?**: More secure than localStorage, sent automatically
- **Why JWT?**: Stateless, works across services
- **Why async?**: Database fetch is non-blocking

## Edge Cases
- Missing token → 401 Unauthorized
- Invalid token → 401 Unauthorized
- Expired token → 401 Unauthorized
- User not found → 404 Not Found
```
