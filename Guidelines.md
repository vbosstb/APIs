# Design Guidelines
## Introduction

<#Platform-Name#> is a collection of reusable services that encapsulate well-defined business capabilities. Developers are encouraged to access these capabilities through Application Programming Interfaces (APIs) that enable consistent design patterns and principles. This facilitates a great developer experience and the ability to quickly compose complex business processes by combining multiple, complementary capabilities as building blocks.

<#Platform-Name#> APIs follow the RESTful architectural style as much as possible. To support our objectives, we have developed a set of rules, standards, and conventions that apply to the design of RESTful APIs. 

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

#### Asynchronous Communication
Synchronous calls to remote systems can lead to threads in waiting state until the call times out. This can completely paralyze a system, as more and more threads move into that state until the system can no longer react to new requests. Further synchronous calls are blocking and prevent the thread from doing anything else.
We reduce the impact of remote failures by communicating asynchronously via events, where possible. Microservices publish streams of events to an event broker, which interested microservices consume asynchronously. Communication with other systems becomes non-blocking: even when some functionality is affected temporarily, the system continues working.
#### Service Degradation
In synchronous calls, we react to remote dependency failures by degrading a service until the remote dependency is working again. This means that your system must be aware of remote dependency health. It needs to detect failure and also notice when an external dependency becomes healthy again.
In asynchronous processing scenarios, you usually have more flexibility in processing service delivery time. Hence, you can live with temporary remote failures and delay processing with retries. However, service degradation is also an option here, if otherwise processing delays due to remote failures are not acceptable.
#### Use Low-Tech Coupling
Low-tech coupling reduces issues resulting from changes in communicating systems, and can reduce complexity and dependencies. An example of low-tech coupling is service discovery via DNS. Communication should be done over interoperable protocols like HTTP instead of, for example, RMI.

### Size
Build services based on domain model and around business entities with state and behavior —- for example “orders”, “payments” or “prices”. In REST terms, these are “resources”.
A service should be big enough to offer a valid business capability, but small enough to be handled by a team that can be fed by two pizzas (Amazon’s Two-Pizza Team rule) -- from two to 12 people. In practice, a Two-Pizza Team may be able to own and run a large number of small services, or a smaller number of larger services.
All things considered, we prefer smaller services written in expressive programming languages with minimal code whenever possible.

### Encapsulation
A domain service can access data and functionality it does not own through other service contracts only.

A service exposes functionality that comprises the functionality and data it owns and implements, as well as the functionality and data it depends upon which it does not own. This principle advocates that any functionality or data that a service depends on and which it does not own must be accessed through service contracts only.

This principle implies the following:
- A service has a clear isolation boundary - a clear scope of ownership in terms of functionality and data
- A service cannot expose the data it does not own directly

### Stability
Service contracts must be stable.

Services must be designed in such a way that the contract they expose remains valid for existing customers. Should the service contract need to evolve in an incompatible fashion for the consumer, this should be communicated clearly.

This principle implies the following:
- Existing clients of a service must be supported for a documented period of time
- Additional functionality must be introduced in a way that does not impact existing consumers
- Deprecation and migration policies must be clearly stated to set consumers' expectations

### Re-Usability
Services must be developed to be reusable across multiple contexts and by multiple consumers.

The main goal of an API platform is to enable applications to be developed quickly and cost effectively by using and combining services. This is possible only if the service contracts have been developed with flexibility for multiple use-cases and multiple consumers. This principle advocates that services be developed in a manner that enables them to be used by multiple consumers and in multiple contexts, some of which may evolve over time.

This principle implies the following:
- A service contract should be designed for not just the immediate context, but with support and/or extensibility to be used by multiple consumers in different contexts
- A service contract may need to incrementally evolve to support multiple contexts and consumers over time

### Contract based
Functionality and data must only be exposed through standardized service contracts.

A service exposes its purpose and capabilities via a service contract. A service contract comprises of functional aspects, non-functional aspects (such as availability, response-time), and business aspects (such as cost-per-call, terms and conditions). Standardized means that the service contracts must be compliant with the contract design standards.

This principle advocates that all functionality and data must only be exposed through standardized service contracts. Consumers of services can, therefore, understand and access functionality and data only through service contracts.

This principle implies the following:
- Functionality and data cannot be understood or accessed outside of service contracts
- Each piece of data (such as that managed in a datastore) is owned by only one service

### Consistency
Services must follow a common set of rules, interaction styles, vocabulary and shared types.

A set of rules prescribes the definition of services in order to expose those in a consistent manner. This principle increases the ease of use of the API platform by reducing the learning curve for consumers of new services.

This principle implies the following:
- A set of standards is defined for services to comply with
- A service should use vocabulary from common and shared dictionaries
- Compatible interaction styles, service granularity and shared types are key for full interoperability and ease of service compositions

### Ease of Use
Services must be easy to use and compose in consumers (and applications).

A service that is difficult and time consuming to use reduces the benefits of a microservices architecture by encouraging consumers to find alternate mechanisms to access the same functionality. Composability means that services can be combined easily because the service contracts and access protocols are consistent, and each service contract does not have to be understood differently. 

This principle implies the following:
- A service contract is easily discoverable and understandable
- Service contracts and protocols are consistent on all aspects that they can be - e.g. identification and authentication mechanisms, error semantics, common type usage, pagination, etc.
- A service has clear ownership, so that consumer providers can reach service owners regarding SLAs, requirements, issues
- A consumer provider can easily integrate, test, and deploy a consumer that uses this service
- A consumer provider can easily monitor the non-functional aspects of a service

### Externalizable
Service must be designed so that the functionality it provides is easily externalizable.

A service is developed for use by consumers that may be from another domain or team, another business unit or another company. In all of these cases, the functionality exposed is the same; what changes is the access mechanism or the policies enforced by a service, like authentication, authorization and rate-limiting. Since the functionality exposed is the same, the service should be designed once and then externalized based on business needs through appropriate policies.

This principle implies the following:
- The service interface must be derived from the domain model and the intended use-cases it is meant to support
- The service contract and access (binding) protocols supported must meet the consumer's needs
- The externalization of a service must not require reimplementation, or a change in service contract

## API Guidelines
### General
#### MUST: Follow API First Principle	12
#### MUST: Provide API Specification using OpenAPI	12
#### Should: Use a Source Code Management System	13
#### MUST: Provide API User Manual	13
#### MUST: Write APIs in U.S. English	13

### Meta Information
#### Should: Contain API Meta Information	13
#### Should: Use Semantic Versioning	13
#### Should: Provide API Audience	14
#### MUST: Provide API Identifiers	14

### Security. 

### Compatibility
#### MUST: Do Not Break Backward Compatibility	14
#### Should: Prefer Compatible Extensions	15
#### Should: Prepare Clients To Not Crash On Compatible API Extensions	15
#### Should: Design APIs Conservatively	16
#### Should: Always Return JSON Objects As Top-Level Data Structures To Support Extensibility	16
#### MUST: Avoid Versioning	17
#### Should: Use URI Versioning	17

### Deprecation
#### MUST: External Partners Must Agree on Deprecation Timespan	17
#### Should: Obtain Approval of Clients	17
#### Should: Reflect Deprecation in API Definition	17
#### Should: Monitor Usage of Deprecated APIs	18
#### Should: Add a Warning Header to Responses	18
#### Should: Add Monitoring for Warning Header	18
#### MUST: Not Start Using Deprecated APIs	18

### JSON Guidelines
#### Should: Property names must be ASCII snake_case	18
#### Should: Array names should be pluralized	18
#### Should: Boolean property values must not be null	18
#### Should: Null values should have their fields removed	18
#### Should: Empty array values should not be null	19
#### Should: Enumerations should be represented as Strings	19
#### Should: Date property values should conform to RFC 3339	19
#### Should: Time durations and intervals could conform to ISO 8601	19
#### Should: Standards could be used for Language, Country and Currency	19

### API Naming
#### MUST: Use lowercase separate words with hyphens for Path Segments	19
#### MUST: Use snake_case (never camelCase) for Query Parameters	20
#### Should: Prefer Hyphenated-Pascal-Case for HTTP header Fields	20
#### MUST: Pluralize Resource Names	20
#### Should: Avoid Trailing Slashes	20
#### May: Use Conventional Query Strings	20

### Resources
#### MUST: Avoid Actions — Think About Resources	20
#### Should: Model complete business processes	21
#### Should: Define useful resources	21
#### MUST: Keep URLs Verb-Free	21
#### Should: Use Domain-Specific Resource Names	21
#### Should: Identify resources and Sub-Resources via Path Segments	21
#### Should: Only Use UUIDs If Necessary	22
#### May: Consider Using (Non-) Nested URLs	22
#### Should: Limit number of Resource types	22
#### Should: Limit number of Sub-Resource Levels	23

### HTTP
#### MUST: Use HTTP Methods Correctly	23
#### Should: Fulfill Safeness and Idempotency Properties	26
#### Should: Use Specific HTTP Status Codes	26
#### Should: Specify Success and Error Responses	29
#### Should: Use 207 for Batch or Bulk Requests	30
#### Should: Use 429 with Headers for Rate Limits	30
#### Should: Explicitly define the Collection Format of Query Parameters	31

### Performance
#### Should: Reduce Bandwidth Needs and Improve Responsiveness	31
#### May: Use gzip Compression	31
#### Should: Support Filtering of Resource Fields	32
#### Should: Allow Optional Embedding of Sub-Resources	33
#### Should: Document Caching, if Supported	33

### Pagination
#### Should: Support Pagination	34
#### Should: Prefer Cursor-Based Pagination, Avoid Offset-Based Pagination	34
#### May: Use Pagination Links Where Applicable	35

### Hypermedia
#### Must: Use REST Maturity Level 2	35
#### May: Use REST Maturity Level 3 - HATEOAS	36
#### Should: Use full, absolute URI	36
#### Should: Use Common Hypertext Controls	36
#### Should: Use Simple Hypertext Controls for Pagination and Self-References	37
#### Should: Not Use “Link Headers” with JSON entities	37

### Data formats
#### Should: Use JSON to Encode Structured Data	37
#### May: Use non JSON Media Types for Binary Data or Alternative Content Representations	38
#### Should: Use Standard Date and Time Formats	38
#### Should: Use Standards for Country, Language and Currency Codes	38
#### Should: Define Format for Type Number and Integer	38
#### Should: Prefer standard Media type name application/json	39

### Common Data Types
#### Should: Use a Common Money Object	39
#### Should: Use common field names and semantics	40
#### Should: Follow Hypertext Control Conventions	42
#### Should: Use Problem JSON	42
#### MUST: Do not expose Stack Traces	43

### Common Headers
#### Should: Use Content Headers Correctly	43
#### Should: Use Standardized Headers	43
#### May: Use Content-Location Header	43
#### Should: Use Location Header instead of Content-Location Header	44
#### May: Use the Prefer header to indicate processing preferences	44
#### May: Consider using ETag together with If-(None-)Match header	44

### API Operations
#### Must: Publish OpenAPI Specification	45
#### Should: Monitor API Usage	45
