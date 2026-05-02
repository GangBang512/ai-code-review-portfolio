# Evaluation Report — Rubric-Based

**Task Type:** REST API Design — Todo Application with Users
**Rubrics Applied:** RESTful Design · Completeness · Security · Production Readiness

---

## Rubric 1: RESTful Design — 4.5 / 5

### What Was Done Well
| Criterion | Evidence |
|---|---|
| Plural resource nouns | `/todos`, `/users` ✅ |
| No verbs in resource URLs | All routes use HTTP method as the verb ✅ |
| Correct HTTP verb semantics | `PATCH` for partial update, `DELETE` for removal, `POST` for creation ✅ |
| Query params for filtering/pagination | `GET /todos?status=pending&page=1&limit=20` ✅ |
| Stateless design | JWT-based authentication (no server-side session) ✅ |
| `/users/me` convention | Widely accepted shorthand for "current authenticated user" ✅ |

### Gaps
- **Todos are not scoped in the URL** — `GET /todos` relies on the JWT to determine user ownership. A stricter REST design would use `/users/me/todos` to make the resource hierarchy explicit. This is a design trade-off, not a clear error, but worth noting.
- **No HATEOAS links** — not required for a basic design, but absent.
- **`/auth/*` endpoints** are not strictly RESTful (they're action-based), but this is a widely accepted and pragmatic pattern for auth.

---

## Rubric 2: Completeness — 3 / 5

### What Was Covered
- Full CRUD for todos ✅
- Full auth lifecycle: register, login, token refresh ✅
- User profile read and update ✅
- Pagination and status filtering ✅
- Response and error envelope formats ✅
- Status code reference ✅

### Significant Gaps
| Missing Item | Impact |
|---|---|
| **No request body schemas** for `POST /todos`, `PATCH /todos/:id` | High — consumers don't know what fields to send |
| **No todo field definitions** (title, description, dueDate, status enum values) | High — the data model is completely absent |
| **No API versioning** (`/api/v1/`) | High — no strategy for non-breaking evolution |
| **No logout / token revocation** | Medium — JWT blacklisting on sign-out is a real requirement |
| **No password reset / change flow** | Medium — standard for any user-auth system |
| **No sort parameter** (`?sort=createdAt`) | Low |
| **No bulk operations** | Low |

---

## Rubric 3: Security — 3 / 5

### What Was Done Well
| Criterion | Evidence |
|---|---|
| JWT-based stateless auth | `/auth/login` returns JWT ✅ |
| Token refresh pattern | `/auth/refresh` ✅ |
| `401 vs 403` distinction | Unauthorized vs Forbidden — correctly separated ✅ |
| Rate limiting awareness | `429 Too Many Requests` in status codes ✅ |
| Safe error format | Error codes are machine-readable, not stack traces ✅ |

### Security Gaps
| Missing Item | Severity |
|---|---|
| **No HTTPS requirement stated** | Critical — all JWT tokens travel in plaintext over HTTP without it |
| **No authorization enforcement statement** | High — "user's todos" is implied, but no explicit statement that users cannot access other users' todos |
| **No refresh token rotation / invalidation strategy** | High — refresh tokens are long-lived; stolen tokens need a revocation path |
| **No token expiry policy** (access token lifetime, refresh lifetime) | Medium |
| **No input validation / sanitization mention** | Medium — the error format hints at it, but no explicit statement |
| **No CORS policy** | Medium — required for any browser-based client |
| **No password security requirements** (hashing algorithm, minimum strength) | Medium |
| **No mention of token storage recommendations** | Low |

---

## Rubric 4: Production Readiness — 3.5 / 5

### What Was Done Well
| Criterion | Evidence |
|---|---|
| Pagination support | `page`, `limit`, `total` in meta ✅ |
| Rate limiting signaled | `429` status code included ✅ |
| Consistent response envelope | `data` + `meta` on all responses ✅ |
| Structured, machine-readable errors | `code` + `message` + `details[]` ✅ |

### Production Gaps
| Missing Item | Severity |
|---|---|
| **No API versioning** (`/v1/`) | Critical — zero path to non-breaking API evolution in production |
| **No health check endpoint** (`GET /health` or `GET /ping`) | High — required for load balancers, k8s liveness probes, uptime monitors |
| **No idempotency key for `POST /todos`** | Medium — duplicate request protection for unreliable networks |
| **No caching headers** (ETag, Cache-Control, Last-Modified) | Medium — important for GET performance at scale |
| **No correlation / request ID** in responses | Medium — essential for distributed tracing and debugging |
| **No OpenAPI/Swagger documentation mention** | Medium — industry standard for production APIs |
| **No deprecation strategy** | Low — how will breaking changes be communicated? |
| **No mention of logging / monitoring contract** | Low |

---

## Score Summary

| Rubric | Score | Grade |
|---|---|---|
| RESTful Design | 4.5 / 5 | Excellent |
| Completeness | 3.0 / 5 | Adequate |
| Security | 3.0 / 5 | Needs Work |
| Production Readiness | 3.5 / 5 | Developing |
| **Overall** | **3.5 / 5** | **Good Foundation** |

---

## Overall Verdict

This is a **well-structured starting point** that demonstrates strong REST fundamentals and a professional response/error envelope contract. However, it reads more like an **API sketch** than a production-ready specification. The three biggest gaps that must be addressed before real engineering work begins are:

1. **Request body schemas and todo field definitions** — the data model is completely absent
2. **API versioning strategy** — non-negotiable for any API expected to evolve
3. **Security documentation** — HTTPS requirement, authorization enforcement, CORS, and token lifecycle are all unaddressed
