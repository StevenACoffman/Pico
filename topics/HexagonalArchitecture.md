# The Four Architectures that will inspire your programming

This is the Big 4. The architectures that may help your work. They may influence the way you think about programming.

1. [HexagonalArchitecture](#1-the-hexagonal-architecture)
2. [CleanArchitecture](#2-the-clean-architecture)
3. [DDD/CQRS](#3-dddcqrs)
4. [DCI](#4-dci)

# 1. The Hexagonal Architecture

(also known as the Ports and Adapters)

Originally described by Alistair Cockburn in http://alistair.cockburn.us/Hexagonal+architecture.
Enhanced and popularised by Steve Freeman and Nat Pryce, thanks to their book: http://www.growing-object-oriented-software.com/
so this is also known as GOOS.

They recommend an outside-in approach to developing systems. You start at the interface and work your way in to the business rules.

![alt text](https://cdn.rawgit.com/StevenACoffman/Pico/master/topics/images/ports-and-adapters-architecture.svg "Hexagonal Architecture")

# 2. The Clean Architecture

Originally described as BCE by Ivar Jacobson from his book Object Oriented Software Engineering: A Use-Case Driven Approach, but enhanced and popularised by Robert "Uncle Bob" Martin: [The Clean Architecture](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
Also see:
* [NODB](https://blog.8thlight.com/uncle-bob/2012/05/15/NODB.html)
* [Earlier Clean Architecture](https://blog.8thlight.com/uncle-bob/2011/11/22/Clean-Architecture.html)
* [Screaming Architecture](https://blog.8thlight.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
* [The Chicken or The Road](https://blog.8thlight.com/uncle-bob/2014/01/27/TheChickenOrTheRoad.html)
* [Clean Microservice](http://blog.cleancoder.com/uncle-bob/2014/10/01/CleanMicroserviceArchitecture.html)
* [Obvious Architecture - Ruby](http://retromocha.com/obvious/)
* [Viper - iOS](https://www.objc.io/issues/13-architecture/viper/)
Bob Martin prefers an inside-out approach compared to GOOS/Hexagonal. He likes to focus on the business rules first, and then put a UI around it later.

The center of your application is also not the database. Nor is it one or more of the frameworks you may be using. The center of your application are the use cases of your application. Persistence and UI are both annoying details that should be easily replaced.

---

> Applications can be mapped into several broad classifications. For example, our application might be a request/response system, like nearly all websites. Or it might be an event driven system, like most computer games. Or it might be a batch processing system, like most old banking and manufacturing applications.

> These classifications have nothing to do with the business rules, nor the problem domain. And yet these classifications have a profound effect on the shape and design of the software. That shape, that design, is architecture.

> If you were to look at two completely different event driven applications, say Quicken and Minecraft, the odds are that you would find a lot of similarities at the highest levels. The same should be true of any two request/response systems, or two batch systems. At their highest levels, there will be similarities in shape, flow, and structure.

> This shape is the system architecture, and it is dictated by the desired user experience -- not by the problem domain. I could, for example, write an accounting application, like Quicken, in a batch style. I could, though it would be a deep shame, write an adventure program like Minecraft, in a request/response style. So architecture and problem domain are discontinuous -- they do not form a continuum -- they differ in kind not just in level.

> This discontinuity means that, although we can use TDD to help us with the design of the problem domain, we cannot use it to help us with the architecture. TDD can't even be begun until we know the shape of the system that is to be created.

![alt text](https://github.com/StevenACoffman/Pico/raw/master/topics/images/CleanArchitecture.jpg "Bob Martin Clean Architecture")

# 3. DDD/CQRS

DDD: [Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design) by Eric Evans
CQRS: [Command Query Responsibility Segregation](http://martinfowler.com/bliki/CQRS.html) by Greg Young (popularised by Martin Fowler)

Two separate concepts, but together create quite a unique architecture:
* Domain Driven Design is a philosophy and methodology in which your domain model should represent the business problems that your app is actually solving
* Command Query Responsibility Segregation is the strategy of separating commands from queries, as opposed to CRUD functionality which is represented in the same object.

# 4. DCI

[DCI: Data Context Interaction](https://en.wikipedia.org/wiki/Data,_context_and_interaction)

James Coplien, Trygve Reenskaug (aka inventor of MVC)
Desribed in James' book: http://www.leansoftwarearchitecture.com/ and here: http://www.artima.com/articles/dci_vision.html

The paradigm separates the domain model (data) from use cases (context) and Roles that objects play (interaction). DCI is complementary to model–view–controller (MVC). MVC as a pattern language is still used to separate the data and its processing from presentation.

In summary, DCI architectures tend to be characterized by the following properties:

* The Data model reflects the domain structure rather than partitions of its behavior;
* Objects dynamically take on Roles during use case enactments;
* Every Role of a use case is played by an object determined by the Context at the start of the use case enactment;
* The network of Interactions between Roles in the code (i.e., at coding time) is the same as the corresponding network of objects at run time;
* These networks are potentially re-created on every use case enactment;
* Roles come in and out of scope with use case lifetimes, but objects that may play these Roles may persist across multiple use case lifetimes and may potentially play many Roles over their own lifetime.

![alt text](https://cdn.rawgit.com/StevenACoffman/Pico/master/topics/images/DCI.svg "Reenskaug DCI")

## Why do we care?

This is why:
![alt text](https://github.com/StevenACoffman/Pico/raw/master/topics/images/longevity.jpg "Bitovi JavaScript Longevity")
(This image from [Bitovi here](http://blog.bitovi.com/longevity-or-lack-thereof-in-javascript-frameworks/).)

Someday Struts 1 on Resin will be considered obsolete (gasp!), and we may need to change our framework. We may even need to change our persistence not to use iBatis. Our Applications may well outlive a particular technology, framework, or even our tenure at an organization. Since TDD cannot help us with the architecture, all developers need to have some sort of architecture in mind.
