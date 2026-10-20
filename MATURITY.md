# Richardson Maturity Model — Task Manager REST API Evaluation

**Student:** Kreshvi (24AIML008)  
**Subject:** Advanced Web Development Frameworks (ITUE301)  
**Assignment:** Week 4 — Richardson Maturity Model

## Evaluation table

| Level | Criterion | Does this API satisfy it? | Evidence |
| --- | --- | --- | --- |
| 0 — The Swamp of POX | One endpoint and one HTTP method typically used for every action. | Not the API's design | Separate resource URIs and different verbs rather than a single RPC endpoint. |
| 1 — Resources | Distinct URIs identify API resources. | Yes | Collection `/tasks` and individual resources `/tasks/:id`. |
| 2 — HTTP verbs | Correct HTTP methods and meaningful status codes. | Yes | GET 200, POST 201, PUT 200, DELETE 200, invalid ID 400, missing resource or route 404, incorrect Content-Type 415, server error 500. |
| 3 — HATEOAS | Responses expose discoverable links to possible actions. | No | Responses do not contain `_links`; see awareness example below. |

**Current highest attained level: Level 2.** Resources have distinct URLs and use standard verbs and codes. Hypermedia navigation is not implemented, so the API is not Level 3.

## Endpoint-level review

| Endpoint | Method | Status codes | Level 2 |
| --- | --- | --- | --- |
| `/tasks` | GET | 200 | Pass |
| `/tasks/:id` | GET | 200, 400, 404 | Pass |
| `/tasks` | POST | 201, 400, 415 | Pass |
| `/tasks/:id` | PUT | 200, 400, 404, 415 | Pass |
| `/tasks/:id` | DELETE | 200, 400, 404 | Pass |
| Unknown route | Any | 404 | Pass |
| Unexpected failure | Any | 500 | Pass |

## Improvements verified

- POST uses `201 Created` and sets a `Location` header.
- GET, POST, PUT and DELETE use the expected HTTP verbs.
- Validation errors return structured 400, 404 and 415 responses.
- The global error handler is last and does not expose raw stack traces.

## HATEOAS awareness (Level 3 example, not implemented)

```json
{
  "id": 123,
  "title": "Task A",
  "completed": false,
  "_links": {
    "self": {"href": "/tasks/123", "method": "GET"},
    "delete": {"href": "/tasks/123", "method": "DELETE"}
  }
}
```

A client could follow these links to retrieve or delete the task without constructing URLs itself. This is a proposed improvement only; the assignment does not require implementation.

## Why production APIs often stop at Level 2

Level 2 delivers predictable resource URLs, standard HTTP methods and status codes. This makes APIs straightforward to document, test and use. Level 3 adds discoverability but also raises implementation and client complexity. For well-documented APIs with established clients, Level 2 may be sufficient.

## Reflection / troubleshooting

Returning JSON does not automatically make an API RESTful; proper resources and HTTP semantics matter. Invalid task IDs should receive 400, nonexistent records 404, and incorrect JSON Content-Type 415. The task manager intentionally uses an in-memory array, meaning task changes are lost when the server restarts.

## Run and test

Requires Node.js 18+. Run `npm install` and `npm start`. Test with Thunder Client/Postman or run `npm test` to execute automated API checks.
