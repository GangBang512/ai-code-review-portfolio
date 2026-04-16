# Common AI Pitfalls in JavaScript / TypeScript

Modern JavaScript, especially in environments like React or Node.js, presents unique challenges for AI models. Here are the most common evaluation flags I raise.

## 1. Array/Object Mutation in React State
**The Problem:** AI models often apply standard imperative JavaScript to declarative frameworks.
```javascript
// Bad AI Code
const handleAdd = (newItem) => {
    stateArray.push(newItem);
    setStateArray(stateArray);
}
```
**The Implication:** React relies on referential equality (shallow comparison) to trigger re-renders. Pushing to an array mutates the existing reference, failing to trigger a UI update.
**My Corrective Feedback:** Enforce immutable updates using the spread operator or functional state updates.
```javascript
// Expected Standard
const handleAdd = (newItem) => {
    setStateArray(prev => [...prev, newItem]);
}
```

## 2. Promise Chaining & Async Boundaries
**The Problem:** Mixing `async/await` with `.then().catch()` incorrectly, or failing to await promises in a loop.
```javascript
// Bad AI Code
async function processAll(items) {
    items.forEach(async (item) => {
        await process(item); // Process is awaited, but the loop doesn't wait!
    });
    console.log("Done"); // Executes before items are processed.
}
```
**The Implication:** The parent function resolves before the asynchronous work is complete, leading to race conditions and "Unhandled Promise Rejections" in Node.js.
**My Corrective Feedback:** Penalize `forEach` with async callbacks. Enforce `for...of` loops or `Promise.all()`.

## 3. Floating Point Math
**The Problem:** AI handling financial data using native JS numbers.
```javascript
// Bad AI Code
const total = price * 1.05; // Adding 5% tax
```
**The Implication:** JavaScript uses IEEE 754 floating-point numbers. `0.1 + 0.2 === 0.30000000000000004`. This leads to catastrophic accounting errors.
**My Corrective Feedback:** Flag for usage of `BigDecimal` libraries or integer-based cent manipulation for currency.

## Summary
In JS/TS, the primary review focus is on asynchronous flow control, immutable state management, and avoiding the quirks of weak typing.
