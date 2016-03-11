Test Suite Organization


| Test | Value Type | Concern |
|---|---|---|
| Adapter | Confidence | Safeguard our use of 3rd-party code |
| Adapter | Understanding | Narrowly specify our dependencies |
| Consumption | Confidence | Safely change and refactor |
| Consumption | Understanding | Make public API easy to use |
| Contract | Confidence | Ensure behavior of others' services |
| Contract | Understanding | Validate our code is useful to others | 
| Discovery | Understanding | Grow a maintainable private API |
| Discovery | Confidence | Know that my code works |
| SAFE | Understanding | Keep the product simple |
| SAFE | Confidence | Verify that production is working | 

[SAFE tests](https://github.com/testdouble/contributing-tests/wiki/SAFE-test):

* [Smoke tests](https://en.wikipedia.org/wiki/Smoke_testing_(software))
* [Acceptance tests](https://en.wikipedia.org/wiki/Acceptance_testing)
* Full-stack tests
* End-to-end tests


Discovery Tests (kinda unit tests):

* Disovery of tiny, boring, consistent units that break big, scary problems into small manageable ones
* Concerned primarily with inputs and outputs (or side effects)
* Secondarily Concerned with basic code design details
* "Code by Wishful Thinking"
* 


1.  Pull down a new feature request that will require the system to do a dozen new things.
2. Identify an entry point for the feature and establish a public-facing contract to get started (e.g. "I'll add a controller action that returns profits for a given month and a year")

2. a. This would also be a good opportunity to encode the public contract in an integration test. This post isn't about integration testing, but I'd recommend a test that both runs in a separate process and uses the application in the same way a real user would (e.g. by sending HTTP requests). Having an integration test for regression safety from the start can help us avoid scratching that itch from our unit tests.

3. Start writing a unit test for the entry point, but instead of immediately trying to solve the problem, intentionally defer writing any implementation logic! Instead, break down the problem by dreaming up all of the objects you wish you had at your disposal (e.g. "This controller would be simple if only it could depend on something that gave it revenue by month and on something else that gave it costs by month").

4. Implement the entry point with TDD by writing your test as if those imagined units did exist. Inject test doubles into the entry point for the dependencies you think you'll need and specify the subject's interaction with the dependencies in your test. Interaction tests specify "collaboration" units which only govern the usage of other units and contain no logic themselves.

5. Repeat steps (3) and (4) for each newly-imagined object, discovering ever-more-fine-grained collaborator objects.

6. Eventually, reach a point where there's no conceivable way to pass the buck any further. At that point, implement the necessary bit of logic in what will become a leaf node in your object graph and recurse up the tree to tackle the next bit of work.
