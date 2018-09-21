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
As mentioned in the introduction, API First is one of our engineering and architecture principles. In a nutshell API First requires two aspects:
- define APIs outside the code first using a standard specification language
- get early review feedback from peers and client developers
By defining APIs outside the code, we want to facilitate early review feedback and also a development discipline that focus service interface design on…
- profound understanding of the domain and required functionality
- generalized business entities / resources, i.e. avoidance of use case specific APIs
- clear separation of WHAT vs. HOW concerns, i.e. abstraction from implementation aspects - APIs should be stable even if we replace complete service implementation including its underlying technology stack
Moreover, API definitions with standardized specification format also facilitate…
- single source of truth for the API specification; it is a crucial part of a contract between service provider and client users
- infrastructure tooling for API discovery, API GUIs, API documents, automated quality checks
An element of API First is to get early review feedback from peers and client developers. Peer review is important for us to get high quality APIs, to enable architectural and design alignment and to supported development of client applications decoupled from service provider engineering life cycle.
It is important to learn, that API First is not in conflict with the agile development principles that we love. Service applications should evolve incrementally — and so its APIs. Of course, our API specification will and should evolve iteratively in different cycles; however, each starting with draft status and early team and peer review feedback. API may change and profit from implementation concerns and automated testing feedback. API evolution during development life cycle may include breaking changes for not yet productive features and as long as we have aligned the changes with the clients. Hence, API First does not mean that you must have 100% domain and requirement understanding and can never produce code before you have defined the complete API and get it confirmed by peer review. On the other hand, API First obviously is in conflict with the bad practice of publishing API definition and asking for peer review after the service integration or even the service productive operation has started. It is crucial to request and get early feedback — as early as possible, but not before the API changes are comprehensive with focus to the next evolution step and have a certain quality (including API Guideline compliance), already confirmed via team internal reviews.

#### MUST: Provide API Specification using OpenAPI
We use the OpenAPI specification (aka Swagger specification) as standard to define API specifications files.

#### Should: Use a Source Code Management System
The API specification files should be subject to version control using a source code management system - best together with the implementing sources. The API specifications of component external APIs must be published and marked with the intended target audience.

#### MUST: Provide API User Manual
In addition to the API Specification, it is good practice to provide an API user manual to improve client developer experience, especially of engineers that are less experienced in using this API. A helpful API user manual typically describes the following API aspects:
- API scope, purpose, and use cases
- concrete examples of API usage
- edge cases, error situation details, and repair hints
- architecture context and major dependencies - including figures and sequence flows
The user manual must be published online, on specific team web servers, or as a Google doc. Please do not forget to include a link to the API user manual into the API specification using the #/externalDocs/url property.

#### MUST: Write APIs in U.S. English
APIs are written in U.S. English.

### Meta Information
#### Should: Contain API Meta Information
API specifications must contain the following OpenAPI meta information to allow for API management:
- #/info/title as (unique) identifying name of the API
- #/info/version to distinguish API specifications versions following semantic rules
- #/info/description containing a proper description of the API
- #/info/contact/{name,url,email} containing the responsible team
- #/info/x-audience intended target audience of the API.
- #/info/x-api-id unique identifier of the API

#### Should: Use Semantic Versioning
OpenAPI allows to specify the API specification version in #/info/version. To share a common semantic of version information we expect API designers to comply to Semantic Versioning 2.0 rules 1 to 8 and 11 restricted to the format <MAJOR>.<MINOR>.<PATCH> for versions as follows:
- Increment the MAJOR version when you make incompatible API changes after having aligned this changes with consumers. (should be avoided)
- Increment the MINOR version when you add new functionality in a backwards-compatible manner.
- Optionally increment the PATCH version when you make backwards-compatible bug fixes or editorial changes not affecting the functionality.
Additional Notes:
- Pre-release versions (rule 9) and build metadata (rule 10) must not be used in API version information.
- While patch versions are useful for fixing typos etc, API designers are free to decide whether they increment it or not.
- API designers should consider to use API version 0.y.z (rule 4) for initial API design.

#### Should: Provide API Audience (TODO)
Each API must be classified with respect to the intended target audience supposed to consume the API, to facilitate differentiated standards on APIs for discoverability, changeability, quality of design and documentation, as well as permission granting. We differentiate the following API audience groups with clear organisational and legal boundaries:
##### component-internal
The API consumers with this audience are restricted to applications of the same functional component. All services of a functional component are owned by specific dedicated owner and engineering team. Typical examples are APIs being used by internal helper and worker services or that support service operation.
##### business-unit-internal
The API consumers with this audience are restricted to applications of a specific product portfolio owned by the same business unit.
##### company-internal
The API consumers with this audience are restricted to applications owned by the business units of the same the company (…)
##### external-partner
The API consumers with this audience are restricted to applications of business partners of the company owning the API and the company itself.
##### external-public
APIs with this audience can be accessed by anyone with Internet access.
Note: a smaller audience group is intentionally included in the wider group and thus does not need to be declared additionally.
Note: Exactly one audience per API specification is allowed. For this reason a smaller audience group is intentionally included in the wider group and thus does not need to be declared additionally. If parts of your API have a different target audience, we recommend to split API specifications along the target audience — even if this creates redundancies (rationale).

#### MUST: Provide API Identifiers
Each API should be identified by an explicit, owner assigned, globally unique, and immutable API identifier. APIs evolve and every API aspect may change, except the API identifier. Based on the API identifier, we can track the API life cycle and manage the history and evolution of an API as a sequence of API specifications. 

### Security. (TODO)
We should at least be protected against the “<a href="https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project">OWASP Top 10 Most Critical Web Application Security Risks</a>”, …more is always better.

### Compatibility
#### MUST: Do Not Break Backward Compatibility
Change APIs, but keep all consumers running. Consumers usually have independent release lifecycles, focus on stability, and avoid changes that do not provide additional value. APIs are contracts between service providers and service consumers that cannot be broken via unilateral decisions.
There are two techniques to change APIs without breaking them:
- follow rules for compatible extensions.
- introduce new API versions and still support older versions.
We strongly encourage using compatible API extensions and discourage versioning. The following guidelines for service providers and consumers enable us (having Postel’s Law in mind) to make compatible changes without versioning.

<b>Note</b>: There is a difference between incompatible and breaking changes. Incompatible changes are changes that are not covered by the compatibility rules below. Breaking changes are incompatible changes deployed into operation, and thereby breaking running API consumers. Usually, incompatible changes are breaking changes when deployed into operation. 
However, in specific controlled situations it is possible to deploy incompatible changes in a non-breaking way, if no API consumer is using the affected API aspects (see also Deprecation guidelines).

<b>Hint</b>: Please note that the compatibility guarantees are for the "on the wire" format. Binary or source compatibility of code generated from an API definition is not covered by these rules. If client implementations update their generation process to a new version of the API definition, it has to be expected that code changes are necessary.

#### Should: Prefer Compatible Extensions
API designers should apply the following rules to evolve RESTful APIs for services in a backward-compatible way:
- Add only optional, never mandatory fields.
- Never change the semantic of fields (e.g. changing the semantic from customer-number to customer-id, as both are different unique customer keys)
- Input fields may have (complex) constraints being validated via server-side business logic. Never change the validation logic to be more restrictive and make sure that all constraints are clearly defined in description.
- Enum ranges can be reduced when used as input parameters, only if the server is ready to accept and handle old range values too. Enum range can be reduced when used as output parameters.
- Enum ranges cannot not be extended when used for output parameters — clients may not be prepared to handle it. However, enum ranges can be extended when used for input parameters.
- Use x-extensible-enum, if range is used for output parameters and likely to be extended with growing functionality. It defines an open list of explicit values and clients must be agnostic to new values.
- Support redirection in case an URL has to change (Moved Permanently ?).

#### Should: Prepare Clients To Not Crash On Compatible API Extensions
Service clients should apply the robustness principle:
- Be conservative with API requests and data passed as input, e.g. avoid to exploit definition deficits like passing megabytes for strings with unspecified maximum length.
- Be tolerant in processing and reading data of API responses, more specifically…
Service clients must be prepared for compatible API extensions of service providers:
- Be tolerant with unknown fields in the payload (see also Fowler’s "TolerantReader" post), i.e. ignore new fields but do not eliminate them from payload if needed for subsequent PUT requests.
- Be prepared that x-extensible-enum return parameter may deliver new values; either be agnostic or provide default behavior for unknown values.
- Be prepared to handle HTTP status codes not explicitly specified in endpoint definitions. Note also, that status codes are extensible. Default handling is how you would treat the corresponding x00 code (see RFC7231 Section 6).
- Follow the redirect when the server returns HTTP status 301 Moved Permanently.

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
