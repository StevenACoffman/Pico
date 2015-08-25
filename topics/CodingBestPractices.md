# Coding Best Practices (compiled and written by Leif Myklebust, Steve Coffman, John Schultz and others)

## Coding Best Practices

There is more than one way to skin a cat. The development team has discovered ways of skinning cats that work better than others for our needs, or that we simply prefer. This is a collection of these habits, and exists to disseminate them and encourage their use wherever applicable in preference to other habits. By being deliberate and consistent about how we do things, our code will have fewer defects and will be easier to read and maintain - both for ourselves and for others.

### Testing
----
#### 1. What to unit test

We do not chase test coverage numbers, but we strive to use TDD wherever we can, and expect our test coverage to reflect this.

At a minimum, we expect to have unit tests covering:
* Methods other than getters/setters in domain objects
* All services, etc.

We’re less interested in unit testing the following, although there are always exceptions:
- Java Config
- DAOs, Spring JPA Repositories, etc.

We've had some debate about unit testing Controllers, as we believe they shouldn't be that interesting (if they are, refactor).

#### 2. Mocking

We use Mockito for mocking (see [Bricks|https://wiki.umms.med.umich.edu/display/MSISDEVEL/Testing+Tools+-+Frameworks+Brick]).

#### 3. When to write integration tests

### Pair Programming

---
Try to pair program as much as possible. Ping-pong pairing (write a test, switch who's driving, make it work, repeat). [Follow pairing best practices.|MSISDEVEL:Pair Programming]

{anchor:msisdig}
### Miss Dig (MSIS DIG)
----
Before you excavate, you call Miss Dig to mark buried gas and power lines. When you are on a Team and faced with something important from a design perspective, that's a similar "MISS DIG" situation, so you should call a quick meeting to check with your team members.

### Blameless Post-Mortems [Please see here|https://codeascraft.com/2012/05/22/blameless-postmortems/]

---

#### Code Review

We Code Review after each story, and we use them as a place to have discussions where we have the opportunity to learn from one another. Mistakes are something to thank people for making, so we can all vicariously learn from them. Solving problems is hard, and after they are already solved, sometimes it is easier to see improved solutions we can all learn from. Everyone has room to improve, and everyone has experience and knowledge worth learning from.

### Java Doc
----
We like java doc on all our classes and methods.

### Comments
----
We don't like comments in the code. If you find yourself writing a comment, consider refactoring your method, perhaps extracting multiple methods, and using well named methods.

### Naming things
----
We like to be verbose. If it takes a few words to describe what a class is, method does, or property is, that's OK. (If it doesn't fit in your IDE window, it's probably too long.)

### Presentation
----
#### JavaScript in separate file

Always put JavaScript in separate files (i.e. avoid inline JavaScript in HTML files). Give any JavaScript file that is unique to the page the same name as the thymeleaf/jsp page.

### Logging
----
[See Logging|MSISDEVEL:Logging] and [More Logging|MSISDEVEL:Application Logging]

### Exception handling
----
### Project configuration
----
#### 1. Multi module maven projects

In a multi module maven project, we put the project version in the parent pom file, as well as dependency versions used in more than one module.

#### 2. Liquibase

#### 3. Properties files

### Deployment
----
#### 1. Datasource configuration

Datasources are configured on Tomcat in the context.xml file for each environment separately. We do not include datasource configuration in the deployed artifact.

### Patterns / Practices
----
#### 1. Null object pattern

### Smells
----
#### 1. Autowired+\+

If you find that you have a lot of Autowires in your class, it's probably time to refactor.
