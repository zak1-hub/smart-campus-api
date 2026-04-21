#REPORT: ANSWERS TO QUESTIONS


###Part 1 — Setup & Discovery

**Q: Explain the default lifecycle of a JAX-RS resource class.

In JAX-RS, resource classes follow a per-request lifecycle by default, meaning a new instance of the class is created every time a request is made. Because of this, any instance variables don’t persist between requests, so they can’t be used to store shared data.

In this project, shared data is instead stored in static fields inside the DataStore class. This allows all resource instances to access the same data regardless of how many objects are created. If instance variables were used instead, each request would effectively start fresh, and previously stored data would not be retained.

One important consideration with this approach is thread safety, since multiple requests could access and modify the shared static data at the same time.

Q: Why is HATEOAS considered a hallmark of advanced RESTful design?

HATEOAS (Hypermedia as the Engine of Application State) is seen as a more advanced REST principle because it makes APIs more dynamic and easier to work with. Instead of clients manually building URLs, the API responses include links to related resources and possible actions.

This means client applications don’t need prior knowledge of all endpoint structures—they can simply follow the links provided. It also makes the API more flexible, because if endpoints change in the future, clients relying on these links won’t need to be updated.

Part 2 — Room Management

Q: What are the implications of returning only IDs versus full room objects?

Returning only IDs is more efficient in terms of bandwidth, especially if there are a large number of rooms. However, it requires the client to make additional requests to retrieve full details, which increases the number of API calls.

On the other hand, returning full room objects increases the size of the response but reduces the need for extra requests. This can be more useful when the client needs all the information straight away.

Overall, the best option depends on the situation—for example, IDs might be better for a simple list view, whereas full objects are more useful for detailed displays.

Q: Is the DELETE operation idempotent in your implementation?

Yes, the DELETE operation is idempotent in this case. When a room is deleted for the first time, the request succeeds and returns a 204 response. If the same request is sent again, it returns a 404 because the room no longer exists.

Even though the responses differ, the final state remains unchanged after the first request—the room is still deleted. This matches the definition of idempotency, where repeating the same request does not change the outcome further.

Part 3 — Sensor Operations

Q: What happens if a client sends data in a format other than JSON?

The use of @Consumes(MediaType.APPLICATION_JSON) ensures that the endpoint only accepts JSON input. If a client sends data in another format, such as XML or plain text, the request will be rejected automatically by JAX-RS.

In this case, the server responds with a 415 Unsupported Media Type error, and the request doesn’t reach the actual resource method. This behaviour is handled by the framework itself, so no extra code is needed.

Q: Why is @QueryParam preferred over path-based filtering?

Query parameters are generally better for filtering because they clearly show that the filter is optional. For example, using /sensors?type=CO2 indicates that we are still working with the sensors collection, just with a filter applied.

If path parameters were used instead (e.g. /sensors/type/CO2), it would suggest that “type” and “CO2” are actual resources, which isn’t accurate—they’re just filter conditions.

Query parameters are also more flexible, as multiple filters can be added easily without making the URL structure messy.

Part 4 — Sub-Resources

Q: What are the architectural benefits of the Sub-Resource Locator pattern?

The Sub-Resource Locator pattern helps organise APIs by splitting functionality across multiple classes. Instead of having one large resource class, different parts of the API are handled separately.

For example, in this project, reading-related logic is handled in SensorReadingResource, while other sensor operations are in SensorResource. This improves code organisation and makes the system easier to maintain and understand.

It also keeps each class focused on a single responsibility, which is especially useful as the API grows in size and complexity.

Part 5 — Error Handling & Logging

Q: Why is HTTP 422 more semantically accurate than 404 for a missing linked resource?

A 404 error usually means the requested endpoint itself doesn’t exist. However, in a case where a client sends valid data to an existing endpoint but references something invalid (like a non-existent room ID), the issue is with the request content rather than the endpoint.

HTTP 422 (Unprocessable Entity) is more appropriate here because the request is valid in structure, but the data it contains doesn’t make sense. In this case, the server understands the request but cannot process it due to invalid references.

Q: What are the cybersecurity risks of exposing Java stack traces?

Returning full Java stack traces to users can create security risks. They can reveal internal details such as class names, package structures, and even library versions.

This information could help attackers identify weaknesses or known vulnerabilities in the system. Stack traces might also expose file paths or parts of the application logic.

To prevent this, the project uses a global exception handler that returns a generic error message instead of exposing sensitive details.

Q: Why use JAX-RS filters for logging instead of manual Logger calls?

Using JAX-RS filters for logging is more efficient than manually adding logging statements in every method. Filters automatically apply to all requests and responses, so there’s no need to repeat code.

This reduces the chance of missing logging in certain endpoints and keeps the resource classes cleaner. It also follows good design principles by separating logging from the main business logic, making the code easier to manage and update.
