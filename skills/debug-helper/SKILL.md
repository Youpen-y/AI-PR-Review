---
name: debug-helper
description: Systematic debugging assistant. Use when user encounters bugs, errors, unexpected behavior, or needs help troubleshooting code issues.
---

# Debug Helper

Systematic debugging approach to identify and fix code issues efficiently.

## When to Use

- User reports a bug or error
- Code behaves unexpectedly
- Application crashes or hangs
- User asks "why isn't this working?"
- User types `/debug` or similar

## Debugging Framework: The 5 Ws

### 1. WHAT - Identify the Problem

Ask clarifying questions:

```
- What exactly is happening?
- What did you expect to happen?
- What error messages do you see?
- What is the actual vs expected output?
```

Run diagnostics:
```bash
# Check logs
tail -f logs/app.log

# Check error output
stderr logs/error.log

# Verify environment
env | grep -i debug
```

### 2. WHERE - Locate the Issue

Narrow down the location:

```bash
# Git bisect for regressions
git bisect start
git bisect bad HEAD
git bisect good <last-known-good>
git bisect run <test-script>

# Search for relevant code
grep -r "error_pattern" src/

# Check recent changes
git log --oneline -10
git diff HEAD~1
```

### 3. WHEN - Understand the Context

Determine conditions:

- Does it happen consistently or intermittently?
- What triggers the issue?
- When did it start happening?
- Under what load/conditions?

Add logging:
```javascript
console.log('DEBUG: Reached point A', { variable });
logger.debug('Function called', { params, timestamp });
```

### 4. WHY - Root Cause Analysis

Investigate the cause:

| Technique | Use Case |
|-----------|----------|
| Rubber Duck | Explain code line by line |
| Minimal Reproducible | Reduce to smallest example |
| Divide & Conquer | Comment out half, repeat |
| Variable Tracing | Log values at each step |
| Stack Analysis | Examine the full stack trace |

### 5. HOW - Fix and Verify

Implement and test:

```bash
# Apply fix
# ...

# Verify fix
npm test
pytest tests/
make test

# Check for regressions
git diff
```

## Debugging Checklist

### Environment
- [ ] Correct version of dependencies?
- [ ] Environment variables set?
- [ ] Configuration file loaded?
- [ ] Sufficient resources (memory, disk)?

### Code Logic
- [ ] Conditionals evaluate correctly?
- [ ] Loop invariants maintained?
- [ ] Edge cases handled?
- [ ] Null/undefined checks present?

### Data Flow
- [ ] Input validation?
- [ ] Data transformation correct?
- [ ] API responses as expected?
- [ ] Database queries return data?

### Concurrency
- [ ] Race conditions possible?
- [ ] Deadlocks detected?
- [ ] Thread safety ensured?
- [ ] Locks used correctly?

## Common Error Patterns

| Error Type | Common Cause | Quick Check |
|------------|-------------|-------------|
| `undefined is not a function` | Wrong type assumed | `typeof variable` |
| `Cannot read property X of undefined` | Missing null check | `variable?.property` |
| `404 Not Found` | Wrong URL/endpoint | Verify URL |
| `500 Internal Server Error` | Unhandled exception | Check server logs |
| `Connection refused` | Service not running | `netstat -tulpn` |
| `Out of memory` | Memory leak | Profile with heapdump |

## Debugging Commands

### General
```bash
# Search logs
grep -i "error" /var/log/app.log

# Monitor file changes
tail -f file.log

# Check process status
ps aux | grep app_name

# Network debugging
curl -v http://localhost:3000/api
netstat -tulpn | grep LISTEN
```

### Node.js
```bash
# Run with debugger
node --inspect-brk app.js

# Check memory
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

# Check import issues
python -c "import module_name"
```

## Questions to Ask the User

When debugging, gather information:

1. **Error Message**: "What's the exact error?"
2. **Reproduction**: "Can you reproduce this consistently?"
3. **Recent Changes**: "What changed before this started?"
4. **Environment**: "OS, runtime version, dependencies?"
5. **Code Context**: "Can you share the relevant code?"

## Response Template

```
## Issue Summary
[Brief description of the problem]

## Possible Causes
1. [Most likely cause]
2. [Alternative cause]
3. [Less likely cause]

## Diagnostic Steps
1. [First check to perform]
2. [Second check]
3. [Verification method]

## Suggested Fix
[Code or configuration change]

## Prevention
[How to avoid similar issues]
```

## Best Practices

- **Start simple**: Check the obvious first
- **Isolate variables**: Change one thing at a time
- **Document findings**: Note what you tried
- **Add tests**: Prevent regression
- **Fix root cause**: Don't just patch symptoms
- **Review thoroughly**: Ensure fix doesn't break else

## Tools to Suggest

| Language | Debugger | Profiler |
|----------|----------|----------|
| JavaScript | Chrome DevTools, node inspect | Chrome Profiler |
| Python | pdb, ipdb | cProfile, py-spy |
| Rust | gdb, lldb | flamegraph |
| Go | delve | pprof |
| Java | jdb | VisualVM |
