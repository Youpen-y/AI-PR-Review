# Performance Optimization Reference Guide

Comprehensive guide to performance analysis, optimization strategies, and best practices.

## Table of Contents

- [Optimization Philosophy](#optimization-philosophy)
- [Profiling and Measurement](#profiling-and-measurement)
- [Algorithm Complexity](#algorithm-complexity)
- [Database Performance](#database-performance)
- [Caching Strategies](#caching-strategies)
- [Memory Management](#memory-management)
- [Concurrency and Parallelism](#concurrency-and-parallelism)
- [Network Optimization](#network-optimization)
- [Frontend Performance](#frontend-performance)

---

## Optimization Philosophy

### The Golden Rules

1. **Measure First**
   ```
   "Premature optimization is the root of all evil" - Donald Knuth

   Wrong: "This loop looks slow, let me optimize it"
   Right: "The profiler shows this function takes 80% of execution time"
   ```

2. **Focus on Hot Paths**
   - 80/20 rule: 20% of code runs 80% of the time
   - Optimize the code that actually executes frequently
   - One-time initialization doesn't need optimization

3. **Set Measurable Goals**
   ```
   Bad: "Make it faster"
   Good: "Reduce API response from 500ms to < 200ms"
   Good: "Handle 10,000 concurrent users"
   ```

### When NOT to Optimize

- Code that runs once (startup, initialization)
- Code already fast enough (< 10ms for user-facing)
- Readability significantly decreased
- Optimization adds complexity without clear benefit

---

## Profiling and Measurement

### Profiling Workflow

```
1. Baseline Measurement
   ↓
2. Identify Bottleneck (profiler)
   ↓
3. Form Hypothesis
   ↓
4. Implement Fix
   ↓
5. Measure Again
   ↓
6. Compare & Verify
```

### Profiling Tools Comparison

| Tool | Language | Type | Best For |
|------|----------|------|----------|
| **Chrome DevTools** | JavaScript | CPU/Memory/Network | Browser apps |
| **Node.js --prof** | Node.js | CPU | Server-side JS |
| **cProfile** | Python | CPU | Python apps |
| **memory_profiler** | Python | Memory | Memory leaks |
| **pprof** | Go | CPU/Memory | Go programs |
| **pprof** | Ruby | CPU/Memory | Ruby apps |
| **JProfiler** | Java | CPU/Memory/Threads | Java apps |
| **flamegraph** | Multiple | Visualization | Hot path analysis |

**Production Monitoring:** For production environments, use APM tools like **Datadog**, **New Relic** (commercial), or **Prometheus** (open source) to continuously monitor performance metrics.

### Key Metrics

| Metric | Definition | Target |
|--------|-----------|--------|
| **Latency** | Time to complete operation | P95 < 200ms (API) |
| **Throughput** | Operations per second | Baseline + improvement |
| **Memory** | RAM consumption | No leaks, < limit |
| **CPU** | Processor usage | < 80% sustained |
| **I/O Wait** | Time waiting for disk/network | Minimize |

### Sample Benchmark Code

```javascript
// Simple benchmark
function benchmark(name, fn, iterations = 10000) {
  const start = performance.now();
  for (let i = 0; i < iterations; i++) {
    fn();
  }
  const end = performance.now();
  const duration = end - start;
  const perOp = duration / iterations;

  console.log(`${name}:`);
  console.log(`  Total: ${duration.toFixed(2)}ms`);
  console.log(`  Per op: ${perOp.toFixed(4)}ms`);
  console.log(`  Ops/sec: ${(1000 / perOp).toFixed(0)}`);
}

// Usage
benchmark('Array push', () => {
  const arr = [];
  arr.push(1);
});

benchmark('Array index', () => {
  const arr = new Array(1000);
  arr[0] = 1;
});
```

---

## Algorithm Complexity

### Big O Reference

| Complexity | Name | Example | n=100 | n=1000 |
|-----------|------|---------|------|-------|
| O(1) | Constant | Hash lookup | 1 | 1 |
| O(log n) | Logarithmic | Binary search | 7 | 10 |
| O(n) | Linear | Simple loop | 100 | 1,000 |
| O(n log n) | Linearithmic | Quick sort | 700 | 10,000 |
| O(n²) | Quadratic | Nested loop | 10,000 | 1,000,000 |
| O(2ⁿ) | Exponential | Recursive Fibonacci | 1.27e30 | - |

### Common Algorithm Replacements

| Problem | Naive (O(n²)) | Optimized (O(n)) |
|---------|--------------|-----------------|
| Find duplicates | Nested loop | Set/Hash |
| Two sum | Nested loop | Hash map |
| Subarray sum | Nested loop | Prefix sum |
| String search | Brute force | KMP/Rabin-Karp |

### Data Structure Selection

| Use Case | Best Structure | Worst Structure |
|----------|---------------|-----------------|
| Lookups by key | Hash Map (O(1)) | Array (O(n)) |
| Ordered traversal | Balanced Tree (O(log n)) | Hash Map (O(n)) |
| Prefix search | Trie (O(k)) | Array (O(n*k)) |
| Priority queue | Heap (O(log n)) | Array (O(n)) |
| Append/remove end | Linked List (O(1)) | Array (O(n)) |

---

## Database Performance

### Query Optimization Checklist

- [ ] Use EXPLAIN/EXPLAIN ANALYZE
- [ ] Add appropriate indexes
- [ ] Avoid SELECT *
- [ ] Use JOIN instead of N+1 queries
- [ ] Use LIMIT for pagination
- [ ] Avoid wildcards at start of LIKE
- [ ] Use connection pooling
- [ ] Consider read replicas

### Index Strategy

```sql
-- Single column index
CREATE INDEX idx_user_email ON users(email);

-- Composite index (order matters!)
CREATE INDEX idx_user_status_date ON users(status, created_at);

-- Covering index (includes extra columns)
CREATE INDEX idx_order_user_total ON orders(user_id) INCLUDE (total, status);

-- Partial index (only index common rows)
CREATE INDEX idx_active_users ON users(email) WHERE status = 'active';

-- Unique index (prevents duplicates)
CREATE UNIQUE INDEX idx_user_email ON users(email);
```

### Query Patterns

**Good:**
```sql
-- Uses index on user_id
SELECT * FROM orders WHERE user_id = 123;

-- Uses composite index
SELECT * FROM orders
WHERE user_id = 123 AND status = 'pending';

-- Uses covering index
SELECT total, status FROM orders WHERE user_id = 123;
```

**Bad:**
```sql
-- Function on column prevents index use
SELECT * FROM users WHERE LOWER(email) = 'test@example.com';

-- Leading wildcard prevents index
SELECT * FROM users WHERE email LIKE '%@example.com';

-- OR doesn't use index well
SELECT * FROM orders WHERE user_id = 123 OR status = 'pending';
```

---

## Caching Strategies

### Caching Layers

```
Application
    ↓ (miss)
L1 Cache (In-memory, e.g., Redis)
    ↓ (miss)
L2 Cache (CDN, e.g., Cloudflare)
    ↓ (miss)
Database
```

### Cache Patterns

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Cache-Aside** | Check cache, if miss, load from DB | General purpose |
| **Write-Through** | Write to cache and DB simultaneously | Strong consistency |
| **Write-Behind** | Write to cache, async to DB | High write volume |
| **Refresh-Ahead** | Update cache before expiration | Predictable access |

### Cache Configuration

```javascript
const cache = new LRU({
  max: 1000,                    // Max entries
  ttl: 1000 * 60 * 15,          // 15 minutes
  updateAgeOnGet: true,         // Extend TTL on access
  updateAgeOnHas: true,         // Extend TTL on check
});
```

### Cache Invalidation

```
"There are only two hard things in Computer Science:
 cache invalidation and naming things." - Phil Karlsson

Strategies:
1. TTL (Time To Live) - Simple, eventual consistency
2. Version-based - Include version in key
3. Event-driven - Invalidate on data change
4. Manual - Explicit invalidation API
```

---

## Memory Management

### Memory Leak Patterns

| Pattern | Cause | Fix |
|---------|-------|-----|
| **Event listeners** | Never removed | Remove listeners |
| **Closures** | Capture large objects | Minimize captured scope |
| **Timers** | Never cleared | clearTimeout/clearInterval |
| **Globals** | Accumulate data | Use scoped variables |
| **Caches** | Unlimited growth | Use LRU with max size |

### Memory Profiling

```javascript
// Node.js heap snapshot
const fs = require('fs');
const v8 = require('v8');

// Take snapshot
const snapshot = v8.writeHeapSnapshot();
fs.writeFileSync('heap.heapsnapshot', snapshot);

// Compare snapshots to find leaks
```

```python
# Python memory profiling
from memory_profiler import profile

@profile
def my_function():
    # Code to profile
    pass

if __name__ == '__main__':
    my_function()
```

### Efficient Data Structures

```javascript
// Bad: Array for lookups
const users = [];
function findUser(id) {
  return users.find(u => u.id === id); // O(n)
}

// Good: Map for lookups
const users = new Map();
function findUser(id) {
  return users.get(id); // O(1)
}

// Bad: Array for uniqueness
const items = [];
function addItem(item) {
  if (!items.includes(item)) { // O(n)
    items.push(item);
  }
}

// Good: Set for uniqueness
const items = new Set();
function addItem(item) {
  items.add(item); // O(1)
}
```

---

## Concurrency and Parallelism

### Concurrency vs Parallelism

| Concurrency | Parallelism |
|-------------|-------------|
| Dealing with multiple tasks at once | Executing multiple tasks simultaneously |
| About structure | About execution |
| Can be single-threaded | Requires multiple cores |

### Patterns

| Pattern | Description | Use When |
|---------|-------------|----------|
| **Thread Pool** | Fixed number of worker threads | CPU-bound tasks |
| **Producer-Consumer** | Queue-based work distribution | Async processing |
| **Fork-Join** | Split work, merge results | Parallel algorithms |
| **Actor Model** | Message-passing actors | Distributed systems |

### Example: Worker Pool (Go)

```go
func worker(id int, jobs <-chan Task, results chan<- Result) {
    for j := range jobs {
        results <- processTask(j)
    }
}

func ProcessParallel(tasks []Task, workers int) []Result {
    jobs := make(chan Task, len(tasks))
    results := make(chan Result, len(tasks))

    // Start workers
    for w := 1; w <= workers; w++ {
        go worker(w, jobs, results)
    }

    // Send jobs
    for _, task := range tasks {
        jobs <- task
    }
    close(jobs)

    // Collect results
    var allResults []Result
    for i := 0; i < len(tasks); i++ {
        r := <-results
        allResults = append(allResults, r)
    }

    return allResults
}
```

---

## Network Optimization

### Reduce Round Trips

```javascript
// Bad: N requests
for (const id of userIds) {
  await fetch(`/api/users/${id}`);
}

// Good: 1 request
await fetch(`/api/users?ids=${userIds.join(',')}`);
```

### Use Batching

```javascript
// Bad: Process one by one
for (const item of items) {
  await api.process(item);
}

// Good: Batch process
const BATCH_SIZE = 100;
for (let i = 0; i < items.length; i += BATCH_SIZE) {
  const batch = items.slice(i, i + BATCH_SIZE);
  await api.processBatch(batch);
}
```

### Compression

```javascript
// Enable gzip compression
import express from 'express';
import compression from 'compression';

const app = express();
app.use(compression());

// Reduces response size by 60-80%
```

---

## Frontend Performance

### Critical Rendering Path

```
1. DOM Construction
2. CSSOM Construction
3. JavaScript Execution
4. Render Tree
5. Layout
6. Paint
```

### Optimization Techniques

| Technique | Impact |
|-----------|--------|
| **Minimize CSS/JS** | Reduce download size |
| **Defer non-critical JS** | Faster initial render |
| **Code splitting** | Load only needed code |
| **Lazy loading images** | Reduce initial page weight |
| **Preload critical resources** | Prioritize important assets |
| **Service workers** | Cache for offline/fast repeat visits |

### Example: Lazy Loading

```javascript
// Lazy load images
const imgObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      imgObserver.unobserve(img);
    }
  });
});

document.querySelectorAll('img[data-src]').forEach(img => {
  imgObserver.observe(img);
});
```

---

## Performance Anti-Patterns

| Anti-Pattern | Problem | Solution |
|-------------|---------|----------|
| Premature optimization | Wastes time, adds complexity | Profile first |
| Micro-optimizations | Minimal gain, less readable | Focus on algorithms |
| Caching everything | Cache invalidation problems | Cache strategically |
| Parallelizing everything | Overhead can be costly | Parallel only when beneficial |
| Ignoring memory | Can cause OOM | Consider memory/CPU trade-off |
| Optimization by guess | Often wrong target | Measure actual bottlenecks |

---

## Quick Reference Checklist

### Before Optimizing
- [ ] Profiled to identify bottleneck
- [ ] Measured baseline metrics
- [ ] Set measurable target
- [ ] Considered if optimization needed

### Implementation
- [ ] Chose appropriate algorithm/data structure
- [ ] Applied appropriate pattern (caching, batching, etc.)
- [ ] Maintained code readability
- [ ] Added comments explaining optimization

### Verification
- [ ] Measured after optimization
- [ ] Confirmed improvement
- [ ] Checked for regressions
- [ ] Tested under load

### Documentation
- [ ] Documented why optimization was needed
- [ ] Noted trade-offs made
- [ ] Included performance metrics
