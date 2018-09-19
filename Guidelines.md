# Design Guidelines
## Introduction

<#Platform-Name#> is a collection of reusable services that encapsulate well-defined business capabilities. Developers are encouraged to access these capabilities through Application Programming Interfaces (APIs) that enable consistent design patterns and principles. This facilitates a great developer experience and the ability to quickly compose complex business processes by combining multiple, complementary capabilities as building blocks.

PayPal APIs follow the RESTful architectural style as much as possible. To support our objectives, we have developed a set of rules, standards, and conventions that apply to the design of RESTful APIs. 

The documentation is as generic as possible to make it easier to incorporate into the guidelines you use in your projects. 
If you have any updates, suggestions, or additions that you would like to contribute, please feel free to comment.

### Contributors

<#User-Name#>

## Table of content

...

## Service Design Principles

### Loose coupling
Coupling refers to a connection or relationship between two things. A measure of coupling is comparable to a level of dependency. This principle advocates the design of service contracts, with a constant emphasis on reducing (loosening) dependencies between the service contract, its implementation, and service consumers.

The principle of Loose Coupling promotes the independent design and evolution of a service’s logic and implementation while still emphasizing baseline interoperability with consumers that have come to rely on the service’s capabilities.

This principle implies the following:
- A service contract should not expose implementation details
- A service contract can evolve without impacting existing consumers
- A service in a particular domain can evolve independently of other domains

### Stability

### Re-Usability

### Contract based

### Consistency

### Ease of Use

### Externalizable

## HTTP Methods, Headers and Statuses

