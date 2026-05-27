## Status codes: 4xx vs 5xx

**4xx = client's fault.** The request itself was the problem. Server functioned correctly and reported back honestly.

**5xx = server's fault.** Server tried to do the right thing and failed.

> Sharper: *4xx = "I understood you and the answer is no." 5xx = "I failed to answer at all."*

### Common 4xx

| Code | Meaning | When |
|---|---|---|
| 400 | Bad Request | Malformed body, wrong shape, validation failed |
| 401 | Unauthorized | No auth / invalid token. "Who are you?" |
| 403 | Forbidden | Authenticated, but not allowed. "I know you, you can't." |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Request conflicts with current state (e.g., duplicate) |
| 422 | Unprocessable Entity | Valid JSON but semantically wrong (alt to 400) |
| 429 | Too Many Requests | Rate limited |

### Common 5xx

| Code | Meaning | When |
|---|---|---|
| 500 | Internal Server Error | Unexpected failure / unhandled exception |
| 502 | Bad Gateway | Upstream service gave a bad response |
| 503 | Service Unavailable | Temporarily down / overloaded |
| 504 | Gateway Timeout | Upstream took too long |

## Success codes worth knowing

| Code | When |
|---|---|
| 200 | OK — success, body returned |
| 201 | Created — POST succeeded, new resource exists |
| 204 | No Content — success, no body returned |
