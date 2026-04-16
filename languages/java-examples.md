# Common AI Pitfalls in Java

Java Enterprise environments demand strict concurrency control and object-oriented memory management. AI models often struggle moving from simple scripting logic to robust thread-safe Java.

## 1. ConcurrentModificationException
**The Problem:** Modifying standard collections while iterating through them.
```java
// Bad AI Code
for (String user : activeUsersList) {
    if (user.equals("inactive")) {
        activeUsersList.remove(user);
    }
}
```
**The Implication:** This throws a `ConcurrentModificationException` immediately. Standard `ArrayList` iterators are fail-fast.
**My Corrective Feedback:** Enforce the use of explicit `Iterator.remove()`, or modern Java 8+ idioms like `activeUsersList.removeIf(u -> u.equals("inactive"));`.

## 2. Reference vs Structural Equality
**The Problem:** Comparing Strings or wrapper classes (like `Integer`) using `==`.
```java
// Bad AI Code
if (userInput == dbPassword) { ... }
```
**The Implication:** `==` compares object references (memory addresses), not the actual text. This will evaluate to `false` for dynamically created strings with identical text, breaking core logic.
**My Corrective Feedback:** Enforce `.equals()` or `Objects.equals(a, b)` for null-safe structural comparison.

## 3. Default Visibility and Encapsulation
**The Problem:** AI often creates classes without access modifiers or makes fields unnecessarily public.
```java
// Bad AI Code
class User {
    String name;  // Default (package-private) visibility
    public int age;
}
```
**The Implication:** Violates core encapsulation principles. Package-private visibility is rarely intended by default, and public fields break the ability to refactor implementations later.
**My Corrective Feedback:** Ensure fields are `private` with appropriate getters/setters, and classes use `public` unless intentionally restricted.

## Summary
Evaluations for Java heavily penalize models that treat it like a scripting language. Thread safety, type correctness, and strict encapsulation are mandatory requirements.
