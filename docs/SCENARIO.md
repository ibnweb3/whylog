# Scenario: the exact messages

All people and reasons are made up for the demo. One person plays the engineer.
Subject repository: https://github.com/ibnweb3/whylog-demo (PR #1 timeout, PR #2 retries).

Post these in `whylog-engineering`, one at a time, waiting for each reply.

## 1. Options from the coder
```
@whylog-coder we merged two changes to whylog-demo: PR #1 added a 5 second timeout and PR #2 added up to 3 retries with backoff. What options did we have for each, and which would you have picked?
```

## 2. The engineer settles it
```
@whylog-coder no need to check the PRs. Good point on the 15s worst case. We kept the single 5s timeout because the client only supports one value, and we kept 3 retries with backoff because our merchant endpoints are flaky.
```

## 3. The scribe records it
```
@whylog-scribe take the scribe role. Please record this decision: the 5s timeout and 3 retries with backoff, in PRs #1 and #2.
```
Expected: the scribe takes the `scribe` role, creates `DR-1 5s timeout and 3 retries with backoff`, marks missing parts "not stated", and asks what would make you revisit it.

```
@whylog-scribe Options rejected: separate connect and read timeouts, because our HTTP client only supports one value. Also a circuit breaker, because it was too heavy for now. Backoff is 200ms then 400ms.
```
```
@whylog-scribe I don't know yet what would make us revisit it.
```
```
!list-documents
```

## 4. Offboarding
```
@whylog-scribe I'm leaving the team next week. List the decisions I own, and interview me about any with gaps.
```
Answer its questions, for example:
```
@whylog-scribe Revisit it if merchants start asking for longer timeouts, or if we move to an HTTP client that supports separate connect and read timeouts.
```
```
@whylog-scribe Retried: network errors, timeouts and 5xx responses. 4xx responses are not retried. There is no jitter, only 200ms then 400ms. We did not add the ~10s total deadline.
```

## 5. Recall, with a record
```
@whylog-recall why do we use a 5 second timeout and 3 retries?
```
Expected: it quotes DR-1, names who said it, links PR #1 and PR #2, and lists what is not stated.

## 6. Recall, with no record
```
@whylog-recall why didn't we use a message queue for webhook delivery instead of retries?
```
Expected: "This was not recorded", the nearest record, and no invented reason.

## Checks
```
!roles
!list-references
!list-documents
```
