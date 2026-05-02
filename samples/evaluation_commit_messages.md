# Evaluation Report — Commit Message Ranking

**Task:** Rank three commit messages for a bug fix (session loss on tab switch due to token refresh race condition)
**Rubrics:** Descriptiveness · Conciseness · Actionability

---

## Ranking: A → C → B

| Rank | Message | Overall |
|------|---------|---------|
| 🥇 #1 | **Message A** — `fix: prevent session loss on tab switch by serializing token refresh` | Best |
| 🥈 #2 | **Message C** — `Fix token refresh race condition` | Good |
| 🥉 #3 | **Message B** — `fixed bug` | Poor |

---

## Rubric Scores

### Descriptiveness — Does it explain what changed and why?

| Message | Score | Evidence |
|---------|-------|----------|
| **A** | 5/5 | Subject line names the symptom ("session loss on tab switch") AND the fix ("serializing token refresh"). Body explains the root cause (concurrent refresh calls invalidating each other), the solution (mutex lock), and the side-effect handling (other tabs wait and reuse the new token). Links to issue `#847`. |
| **C** | 3/5 | Subject names the root cause ("token refresh race condition"). Body explains the user-facing symptom (logged out on tab switch) and the mechanism (concurrent requests). However, it **does not describe the fix itself** — a reader knows *what broke* but not *what was done about it*. |
| **B** | 1/5 | Provides zero information about what changed, why, or where. "fixed bug" could describe any commit in the repository's history. |

---

### Conciseness — Is it appropriately brief without losing meaning?

| Message | Score | Evidence |
|---------|-------|----------|
| **A** | 4/5 | The body is three focused paragraphs — each serving a distinct purpose (root cause, solution, scope). Uses Conventional Commits prefix (`fix:`). Could arguably trim one sentence, but nothing is filler. Slightly verbose but justified by the complexity of the fix. |
| **C** | 5/5 | Two sentences that efficiently communicate the problem. No wasted words. If it had also described the fix, it would be the ideal length. As-is, it's concise — perhaps *too* concise, which costs it on other rubrics. |
| **B** | 2/5 | Two words is not concise — it's *empty*. Conciseness means conveying maximum meaning in minimum words, not using fewer words at the expense of all meaning. It communicates nothing, so its brevity has no value. |

---

### Actionability — Would this help a future developer understand the context?

| Message | Score | Evidence |
|---------|-------|----------|
| **A** | 5/5 | A developer encountering a similar bug (e.g., race condition in another token flow) can use this commit to understand the pattern (mutex-based serialization), find the related issue (#847), and understand the multi-tab interaction model. Highly actionable for debugging, reverting, or extending. |
| **C** | 3/5 | Helps a developer understand *when* the bug occurs (tab switching) and *why* (concurrent invalidation), but doesn't describe the solution. A developer reading this in `git log` would still need to read the diff to understand the fix approach. |
| **B** | 1/5 | Provides no context for any future action — no searchable keywords, no issue link, no symptom description. Useless in `git log`, `git bisect`, or code archaeology. |

---

## Score Summary

| Message | Descriptiveness | Conciseness | Actionability | Average |
|---------|:-:|:-:|:-:|:-:|
| **A** | 5 | 4 | 5 | **4.7** |
| **C** | 3 | 5 | 3 | **3.7** |
| **B** | 1 | 2 | 1 | **1.3** |

---

## Justification (3–4 sentences)

Message A is the clear winner because it follows the Conventional Commits format, names both the symptom and the fix in the subject line, explains the root cause and solution in the body, and links to the tracking issue — giving future developers everything they need without reading the diff. Message C earns second place by correctly identifying the race condition and the user-facing symptom in a focused two-sentence body, but it omits the actual fix (mutex/serialization), which limits its actionability for anyone reviewing `git log` later. Message B is last because "fixed bug" conveys zero information — it fails all three rubrics by providing no searchable keywords, no context, and no path to understanding what changed or why. The gap between A and C is meaningful but recoverable; the gap between C and B is fundamental.
