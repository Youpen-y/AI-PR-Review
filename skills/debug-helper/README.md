# Debug Helper Skill

> Systematic debugging assistant for efficient troubleshooting.

## Overview

This skill provides a structured approach to debugging, helping you identify, diagnose, and fix code issues effectively. It follows the **5 Ws framework** (What, Where, When, Why, How) to ensure comprehensive problem-solving.

## Quick Start

When using Claude Code, simply describe your issue:

```
My app crashes when I try to login
```

```
I'm getting a 500 error on the API endpoint
```

```
This function returns undefined but should return an array
```

Claude will use this skill to guide you through systematic debugging.

## Features

### Systematic Framework

- **WHAT**: Identify the exact problem
- **WHERE**: Locate the issue in code
- **WHEN**: Understand conditions and context
- **WHY**: Root cause analysis
- **HOW**: Fix and verify

### Comprehensive Coverage

- Error pattern recognition
- Language-specific debugging tools
- Common issue detection
- Diagnostic commands reference

### Best Practices

- Rubber duck debugging technique
- Minimal reproducible examples
- Git bisect for regressions
- Logging strategies

## Structure

```
debug-helper/
├── SKILL.md    # Main skill file
└── README.md   # This file
```

## Usage Examples

### JavaScript/Node.js

```bash
# Debug with inspector
node --inspect-brk app.js

# Check memory leaks
node --heap-prof app.js

# Trace warnings
node --trace-warnings app.js
```

### Python

```bash
# Debug with pdb
python -m pdb script.py

# Profile performance
python -m cProfile script.py
```

### General Debugging

```bash
# Search error logs
grep -i "error" /var/log/app.log

# Monitor file changes
tail -f file.log

# Git bisect for regressions
git bisect start
git bisect bad HEAD
git bisect good <last-working>
```

## Debugging Checklist

Use this checklist to systematically diagnose issues:

- [ ] Environment configured correctly?
- [ ] All dependencies installed?
- [ ] Error logs reviewed?
- [ ] Recent changes examined?
- [ ] Input validated?
- [ ] Edge cases considered?
- [ ] Concurrency issues checked?

## Common Error Patterns

| Error | Cause | Solution |
|-------|-------|----------|
| `undefined is not a function` | Wrong type assumed | Add type check |
| `Cannot read property of undefined` | Missing null check | Use optional chaining |
| `404 Not Found` | Wrong endpoint | Verify URL |
| `Connection refused` | Service down | Check process status |
| `Out of memory` | Memory leak | Profile heap usage |

## License

MIT
