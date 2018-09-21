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
#### MUST: Follow API First Principle
#### MUST: Provide API Specification using OpenAPI
#### Should: Use a Source Code Management System
#### MUST: Provide API User Manual
#### MUST: Write APIs in U.S. English

### Meta Information
#### Should: Contain API Meta Information
#### Should: Use Semantic Versioning
#### Should: Provide API Audience
#### MUST: Provide API Identifiers

### Security. 

### Compatibility
#### MUST: Do Not Break Backward Compatibility
#### Should: Prefer Compatible Extensions
#### Should: Prepare Clients To Not Crash On Compatible API Extensions
#### Should: Design APIs Conservatively
#### Should: Always Return JSON Objects As Top-Level Data Structures To Support Extensibility
#### MUST: Avoid Versioning
#### Should: Use URI Versioning

### Deprecation
#### MUST: External Partners Must Agree on Deprecation Timespan
#### Should: Obtain Approval of Clients
#### Should: Reflect Deprecation in API Definition
#### Should: Monitor Usage of Deprecated APIs
#### Should: Add a Warning Header to Responses
#### Should: Add Monitoring for Warning Header
#### MUST: Not Start Using Deprecated APIs

### JSON Guidelines
#### Should: Property names must be ASCII snake_case
#### Should: Array names should be pluralized
#### Should: Boolean property values must not be null
#### Should: Null values should have their fields removed
#### Should: Empty array values should not be null
#### Should: Enumerations should be represented as Strings
#### Should: Date property values should conform to RFC 3339
#### Should: Time durations and intervals could conform to ISO 8601
#### Should: Standards could be used for Language, Country and Currency

### API Naming
#### MUST: Use lowercase separate words with hyphens for Path Segments
#### MUST: Use snake_case (never camelCase) for Query Parameters
#### Should: Prefer Hyphenated-Pascal-Case for HTTP header Fields
#### MUST: Pluralize Resource Names
#### Should: Avoid Trailing Slashes
#### May: Use Conventional Query Strings

### Resources
#### MUST: Avoid Actions — Think About Resources
#### Should: Model complete business processes
#### Should: Define useful resources
#### MUST: Keep URLs Verb-Free
#### Should: Use Domain-Specific Resource Names
#### Should: Identify resources and Sub-Resources via Path Segments
#### Should: Only Use UUIDs If Necessary
#### May: Consider Using (Non-) Nested URLs
#### Should: Limit number of Resource types
#### Should: Limit number of Sub-Resource Levels

### HTTP
#### MUST: Use HTTP Methods Correctly
#### Should: Fulfill Safeness and Idempotency Properties
#### Should: Use Specific HTTP Status Codes
#### Should: Specify Success and Error Responses
#### Should: Use 207 for Batch or Bulk Requests
#### Should: Use 429 with Headers for Rate Limits
#### Should: Explicitly define the Collection Format of Query Parameters

### Performance
#### Should: Reduce Bandwidth Needs and Improve Responsiveness
#### May: Use gzip Compression
#### Should: Support Filtering of Resource Fields
#### Should: Allow Optional Embedding of Sub-Resources
#### Should: Document Caching, if Supported

### Pagination
#### Should: Support Pagination
#### Should: Prefer Cursor-Based Pagination, Avoid Offset-Based Pagination
#### May: Use Pagination Links Where Applicable

### Hypermedia
#### Must: Use REST Maturity Level 2
#### May: Use REST Maturity Level 3 - HATEOAS
#### Should: Use full, absolute URI
#### Should: Use Common Hypertext Controls
#### Should: Use Simple Hypertext Controls for Pagination and Self-References
#### Should: Not Use “Link Headers” with JSON entities

### Data formats
#### Should: Use JSON to Encode Structured Data
#### May: Use non JSON Media Types for Binary Data or Alternative Content Representations
#### Should: Use Standard Date and Time Formats
#### Should: Use Standards for Country, Language and Currency Codes
#### Should: Define Format for Type Number and Integer
#### Should: Prefer standard Media type name application/json

### Common Data Types
#### Should: Use a Common Money Object
#### Should: Use common field names and semantics
#### Should: Follow Hypertext Control Conventions
#### Should: Use Problem JSON
#### MUST: Do not expose Stack Traces

### Common Headers
#### Should: Use Content Headers Correctly
#### Should: Use Standardized Headers
#### May: Use Content-Location Header
#### Should: Use Location Header instead of Content-Location Header
#### May: Use the Prefer header to indicate processing preferences
#### May: Consider using ETag together with If-(None-)Match header

### API Operations
#### Must: Publish OpenAPI Specification
#### Should: Monitor API Usage
