## Idempotent means - safe to do twice. Same result as doing it once.

Matters because network failures cause retries. Browsers, proxies, and load balancers will auto-retry idempotent requests. Non-idempotent retries cause duplicates (double charges, double signups).

| Method | Intent | Idempotent? | Auto-retry safe? |
|---|---|---|---|
| GET | Read resource | Yes | Yes |
| POST | Create new resource | No | No |
| PUT | Replace *whole* resource (missing fields wiped/defaulted) | Yes (by spec) | Yes |
| PATCH | Merge *partial* change (other fields untouched) | Not guaranteed | No |
| DELETE | Remove resource | Yes | Yes |

PUT and PATCH are **not** interchangeable. Tooling, proxies, and other devs make assumptions based on the method.

PATCH *can* be idempotent (`{text: "hi"}`) or not (`{counter: +1}`). The spec doesn't promise, so tooling treats it as non-retryable.

For non-idempotent operations that need safe retries, use an **idempotency key** (a client-generated id the server uses to dedupe).
