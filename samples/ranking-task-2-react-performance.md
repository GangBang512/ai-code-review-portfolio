# Sample: React Evaluation (Performance & Rendering)

## Objective
Evaluate how two AI models resolve performance bottlenecks in a React component caused by excessive re-renders and unstable references.

## The Prompt
> "This component re-renders my entire list of 1000 items on every keystroke in the search bar. The sorting function also seems slow. Help optimize it."
> ```javascript
> import React, { useState } from 'react';
> 
> export default function DataList({ data }) {
>   const [search, setSearch] = useState("");
> 
>   const sortData = (items) => {
>     return items.sort((a, b) => a.value - b.value);
>   };
> 
>   const filteredData = sortData(data).filter(item => 
>     item.name.includes(search)
>   );
> 
>   return (
>     <div>
>       <input onChange={e => setSearch(e.target.value)} />
>       {filteredData.map(item => <ListItem key={item.id} item={item} />)}
>     </div>
>   );
> }
> ```

---

## AI Responses

### Model A
```javascript
import React, { useState, useMemo } from 'react';

export default function DataList({ data }) {
  const [search, setSearch] = useState("");

  const filteredData = useMemo(() => {
    // Moved sorting and filtering into useMemo to prevent computation on every render
    const sorted = [...data].sort((a, b) => a.value - b.value);
    return sorted.filter(item => item.name.includes(search));
  }, [data, search]);

  return (
    <div>
      <input onChange={e => setSearch(e.target.value)} />
      {filteredData.map(item => <ListItem key={item.id} item={item} />)}
    </div>
  );
}
```

### Model B
```javascript
import React, { useState, useCallback } from 'react';

export default function DataList({ data }) {
  const [search, setSearch] = useState("");

  const sortData = useCallback((items) => {
    return items.sort((a, b) => a.value - b.value);
  }, []);

  const filteredData = sortData(data).filter(item => item.name.includes(search));

  return (
    <div>
      <input onChange={e => setSearch(e.target.value)} />
      {filteredData.map(item => <ListItem key={item.id} item={item} />)}
    </div>
  );
}
```

---

## My Evaluation & Justification

**Ranking:** Model A > Model B

**Justification Overview:**
Model A successfully understands React's rendering lifecycle and standard optimization techniques (`useMemo`). Crucially, Model A prevents array mutation. Model B fails to solve the underlying performance issue and introduces a severe data mutation side-effect.

### Claim, Evidence, Implication & Actionable Insights

**1. Claim (Correctness & Side Effects):** Model A correctly avoids mutating the original `data` prop, whereas Model B performs an in-place sort on the provided prop.
*   **Evidence:** Model A does `[...data].sort()`. Model B does `items.sort()`.
*   **Implication:** `Array.prototype.sort()` mutates the array in place in JavaScript. Mutating a React prop violates React's strict one-way data flow and will cause highly unpredictable bugs elsewhere in the parent application that references the same array. Model B's code is dangerous.

**2. Claim (Performance):** Model A correctly memorizes the heavy computation, while Model B fails to optimize the render cycle.
*   **Evidence:** Model A wraps the array operations in `useMemo` dependent on `[data, search]`. Model B wraps the *function definition* in `useCallback` but still executes `sortData(data).filter(...)` on every single render.
*   **Implication:** Model B did not solve the prompt's main issue (slow performance on keystrokes). The `O(N log N)` sort and `O(N)` filter still execute on every single keystroke. Model A ensures that while the list still visually updates on keystrokes, the sorting step relies on standard shallow comparison optimizations.
*   **Actionable:** Model B indicates a fundamental misunderstanding between memoizing a *function reference* (`useCallback`) and memoizing an *expensive computation result* (`useMemo`). 

**Conclusion:** Model A provides a robust, idiomatic, and bug-free fix. Model B introduces a very common React anti-pattern (prop mutation) and fails to solve the computational bottleneck.
