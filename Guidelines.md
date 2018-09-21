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
We use the <a href="http://swagger.io/specification/">OpenAPI specification</a> (aka Swagger specification) as standard to define API specifications files.

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
OpenAPI allows to specify the API specification version in #/info/version. To share a common semantic of version information we expect API designers to comply to <a href="http://semver.org/spec/v2.0.0.html">Semantic Versioning 2.0</a> rules 1 to 8 and 11 restricted to the format <MAJOR>.<MINOR>.<PATCH> for versions as follows:
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
Note: Exactly one audience per API specification is allowed. For this reason a smaller audience group is intentionally included in the wider group and thus does not need to be declared additionally. If parts of your API have a different target audience, we recommend to split API specifications along the target audience — even if this creates redundancies (<a href="https://docs.google.com/document/d/1ff9b6oIa6dyQRyaj36-jmFdtCjWlngnGMsligR4RMIY/edit#heading=h.401bmun50mlj">rationale</a>).

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
- Support redirection in case an URL has to change (<a href="https://en.wikipedia.org/wiki/HTTP_301">Moved Permanently</a> ?).

#### Should: Prepare Clients To Not Crash On Compatible API Extensions
Service clients should apply the robustness principle:
- Be conservative with API requests and data passed as input, e.g. avoid to exploit definition deficits like passing megabytes for strings with unspecified maximum length.
- Be tolerant in processing and reading data of API responses, more specifically…
Service clients must be prepared for compatible API extensions of service providers:
- Be tolerant with unknown fields in the payload (see also Fowler’s "<a  href="http://martinfowler.com/bliki/TolerantReader.html">TolerantReader</a>" post), i.e. ignore new fields but do not eliminate them from payload if needed for subsequent PUT requests.
- Be prepared that x-extensible-enum return parameter may deliver new values; either be agnostic or provide default behavior for unknown values.
- Be prepared to handle HTTP status codes not explicitly specified in endpoint definitions. Note also, that status codes are extensible. Default handling is how you would treat the corresponding x00 code (see <a href="https://tools.ietf.org/html/rfc7231#section-6">RFC7231 Section 6</a>).
- Follow the redirect when the server returns HTTP status <a href="https://en.wikipedia.org/wiki/HTTP_301">301 Moved Permanently</a>.

#### Should: Design APIs Conservatively
Designers of service provider APIs should be conservative and accurate in what they accept from clients:
- Unknown input fields in payload or URL should not be ignored; servers should provide error feedback to clients via an HTTP 400 response code.
- Be accurate in defining input data constraints (like formats, ranges, lengths etc.) — and check constraints and return dedicated error information in case of violations.
- Prefer being more specific and restrictive (if compliant to functional requirements), e.g. by defining length range of strings. It may simplify implementation while providing freedom for further evolution as compatible extensions.
Not ignoring unknown input fields is a specific deviation from Postel’s Law (e.g. see also
<a href="https://cacm.acm.org/magazines/2011/8/114933-the-robustness-principle-reconsidered/fulltext">The Robustness Principle Reconsidered</a>) and a strong recommendation. Servers might want to take different approach but should be aware of the following problems and be explicit in what is supported:
- Ignoring unknown input fields is actually not an option for PUT, since it becomes asymmetric with subsequent GET response and HTTP is clear about the PUT "replace" semantics and default roundtrip expectations (see <a href="https://tools.ietf.org/html/rfc7231#section-4.3.4">RFC7231 Section 4.3.4</a>). Note, accepting (i.e. not ignoring) unknown input fields and returning it in subsequent GET responses is a different situation and compliant to PUT semantics.
- Certain client errors cannot be recognized by servers, e.g. attribute name typing errors will be ignored without server error feedback. The server cannot differentiate between the client intentionally providing an additional field versus the client sending a mistakenly named field, when the client’s actual intent was to provide an optional input field.
- Future extensions of the input data structure might be in conflict with already ignored fields and, hence, will not be compatible, i.e. break clients that already use this field but with different type.
In specific situations, where a (known) input field is not needed anymore, it either can stay in the API definition with "not used anymore" description or can be removed from the API definition as long as the server ignores this specific parameter.

#### Should: Always Return JSON Objects As Top-Level Data Structures To Support Extensibility
In a response body, you must always return a JSON object (and not e.g. an array) as a top level data structure to support future extensibility. JSON objects support compatible extension by additional attributes. This allows you to easily extend your response and e.g. add pagination later, without breaking backwards compatibility.

#### MUST: Avoid Versioning
When changing your RESTful APIs, do so in a compatible way and avoid generating additional API versions. Multiple versions can significantly complicate understanding, testing, maintaining, evolving, operating and releasing our systems (<a href="http://martinfowler.com/articles/enterpriseREST.html">supplementary reading</a>).
If changing an API can’t be done in a compatible way, then proceed in one of these three ways:
- create a new resource (variant) in addition to the old resource variant
- create a new service endpoint — i.e. a new application with a new API (with a new domain name)
- create a new API version supported in parallel with the old API by the same microservice
As we discourage versioning by all means because of the manifold disadvantages, we strongly recommend to only use the first two approaches.

#### Should: Use URI Versioning
With URI versioning a (major) version number is included in the path, e.g. /v1/customers. 
The consumer has to wait until the provider has been released and deployed. If the consumer also supports hypermedia links — even in their APIs — to drive workflows (HATEOAS), this might become complex. 

### Deprecation
Sometimes it is necessary to phase out an API endpoint (or version), for instance, if a field is no longer supported in the result or a whole business functionality behind an endpoint has to be shut down. There are many other reasons as well. As long as these endpoints are still used by consumers these are breaking changes and not allowed. Deprecation rules have to be applied to make sure that necessary consumer changes are aligned and deprecated endpoints are not used before API changes are deployed.

#### MUST: External Partners Must Agree on Deprecation Timespan
If the API is consumed by any external partner, the producer must define a reasonable timespan that the API will be maintained after the producer has announced deprecation. The external partner (client) must agree to this minimum after-deprecation-lifespan before he starts using the API.

#### Should: Obtain Approval of Clients
Before shutting down an API (or version of an API) the producer must make sure, that all clients have given their consent to shut down the endpoint. Producers should help consumers to migrate to a potential new endpoint (i.e. by providing a migration manual). After all clients are migrated, the producer may shut down the deprecated API.

#### Should: Reflect Deprecation in API Definition
API deprecation must be part of the OpenAPI definition. If a method on a path, a whole path or even a whole API endpoint (multiple paths) should be deprecated, the producers must set deprecated=true on each method / path element that will be deprecated (OpenAPI 2.0 only allows you to define deprecation on this level). 
If deprecation should happen on a more fine grained level (i.e. query parameter, payload etc.), the producer should set deprecated=true on the affected method / path element and add further explanation to the description section.
If deprecated is set to true, the producer must describe what clients should use instead and when the API will be shut down in the description section of the API definition.

#### Should: Monitor Usage of Deprecated APIs
Owners of APIs used in production must monitor usage of deprecated APIs until the API can be shut down in order to align deprecation and avoid uncontrolled breaking effects. 
But since ALL API’s should be monitored on usage, this cannot be an issue.

#### Should: Add a Warning Header to Responses
During deprecation phase, the producer should add a Warning header (see <a href="https://tools.ietf.org/html/rfc7234#section-5.5">RFC 7234 - Warning header</a>) field. When adding the Warning header, the warn-code must be 299 and the warn-textshould be in form of "The path/operation/parameter/… {name} is deprecated and will be removed by {date}. Please see {link} for details." with a link to a documentation describing why the API is no longer supported in the current form and what clients should do about it. Adding the Warning header is not sufficient to gain client consent to shut down an API.

#### Should: Add Monitoring for Warning Header
<b>Clients</b> should monitor the Warning header in HTTP responses to see if an API will be deprecated in future.

#### MUST: Not Start Using Deprecated APIs
<b>Clients</b> must not start using deprecated parts of an API.

### JSON Guidelines
These guidelines provides recommendations for defining JSON data. JSON here refers to <a href="http://www.rfc-editor.org/rfc/rfc7159.txt">RFC 7159</a> (which updates <a href="https://www.ietf.org/rfc/rfc4627.txt">RFC 4627</a>), the “application/json” media type and custom JSON media types defined for APIs.
The first some of the following guidelines are about property names, the later ones about values.

#### Should: Property names must be ASCII snake_case
And never camelCase: ^[a-z_][a-z_0-9]*$
Property names are restricted to ASCII strings. The first character must be a letter, or an underscore, and subsequent characters can be a letter, an underscore, or a number.
(It is recommended to use _ at the start of property names only for keywords like _links.)
Rationale: No established industry standard exists, but many popular Internet companies prefer snake_case: e.g. GitHub, Stack Exchange, Twitter. Others, like Google and Amazon, use both - but not only camelCase. It’s essential to establish a consistent look and feel such that JSON looks as if it came from the same hand.

#### Should: Array names should be pluralized
To indicate they contain multiple values prefer to pluralize array names. This implies that object names should in turn be singular.

#### Should: Boolean property values must not be null
Schema based JSON properties that are by design booleans must not be presented as nulls. A boolean is essentially a closed enumeration of two values, true and false. If the content has a meaningful null value, strongly prefer to replace the boolean with enumeration of named values or statuses - for example accepted_terms_and_conditions with true or false can be replaced with terms_and_conditions with values yes, no and unknown.

#### Should: Null values should have their fields removed
Swagger/OpenAPI, which is in common use, doesn’t support null field values (it does allow omitting that field completely if it is not marked as required). However that doesn’t prevent clients and servers sending and receiving those fields with null values. Also, in some cases null may be a meaningful value - for example, JSON Merge Patch RFC 7382) using null to indicate property deletion.

#### Should: Empty array values should not be null
Empty array values can unambiguously be represented as the empty list, [].

#### Should: Enumerations should be represented as Strings
Strings are a reasonable target for values that are by design enumerations.

#### Should: Date property values should conform to RFC 3339
Use the date and time formats defined by <a href="http://tools.ietf.org/html/rfc3339#section-5.6">RFC 3339</a>:
- for "date" use strings matching date-fullyear "-" date-month "-" date-mday, for example: 2015-05-28
- for "date-time" use strings matching full-date "T" full-time, for example 2015-05-28T14:07:17Z
Note that the <a href="https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#data-types">OpenAPI format</a> "date-time" corresponds to "date-time" in the RFC) and 2015-05-28for a date (note that the OpenAPI format "date" corresponds to "full-date" in the RFC). Both are specific profiles, a subset of the international standard <a href="http://en.wikipedia.org/wiki/ISO_8601">ISO 8601</a>.
We encourage restricting dates to UTC and without offsets. For example 2015-05-28T14:07:17Z rather than 2015-05-28T14:07:17+00:00. From experience we have learned that zone offsets are not easy to understand and often not correctly handled. Note also that zone offsets are different from local times that might be including daylight saving time. Localization of dates should be done by the services that provide user interfaces, if required.
When it comes to storage, all dates should be consistently stored in UTC without a zone offset. Localization should be done locally by the services that provide user interfaces, if required.
Sometimes it can seem data is naturally represented using numerical timestamps, but this can introduce interpretation issues with precision - for example whether to represent a timestamp as 1460062925, 1460062925000 or 1460062925.000. Date strings, though more verbose and requiring more effort to parse, avoid this ambiguity.

#### Should: Time durations and intervals could conform to <a href="http://en.wikipedia.org/wiki/ISO_8601">ISO 8601</a>
Schema based JSON properties that are by design durations and intervals could be strings formatted as recommended by <a href="http://en.wikipedia.org/wiki/ISO_8601">ISO 8601</a> (<a href="https://tools.ietf.org/html/rfc3339#appendix-A">Appendix A of RFC 3339 contains a grammar for durations</a>).

#### Should: Standards could be used for Language, Country and Currency
- <a href="https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes">ISO 639-1 language code</a>
- <a href="http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2">ISO 3166-1-alpha2 country</a>
- <a href="http://en.wikipedia.org/wiki/ISO_4217">ISO 4217 currency codes</a>

### API Naming
#### MUST: Use lowercase separate words with hyphens for Path Segments
Example:
  /shipment-orders/{shipment-order-id}
This applies to concrete path segments and not the names of path parameters. For example {shipment_order_id} would be ok as a path parameter.

#### MUST: Use snake_case (never camelCase) for Query Parameters
Examples:
  customer_number, order_id, billing_address

#### Should: Prefer Hyphenated-Pascal-Case for HTTP header Fields
This is for consistency in your documentation (most other headers follow this convention). Avoid camelCase (without hyphens). Exceptions are common abbreviations like “ID.”
Examples:
  Accept-Encoding
  Apply-To-Redirect-Ref
  Disposition-Notification-Options
  Original-Message-ID
See also: <a href="http://tools.ietf.org/html/rfc7230#page-22">HTTP Headers are case-insensitive (RFC 7230)</a>.

#### MUST: Pluralize Resource Names
Usually, a collection of resource instances is provided (at least API should be ready here). The special case of a resource singleton is a collection with cardinality 1.

#### Should: Avoid Trailing Slashes
The trailing slash must not have specific semantics. Resource paths must deliver the same results whether they have the trailing slash or not.

#### May: Use Conventional Query Strings
If you provide query support for sorting, pagination, filtering functions or other actions, use the following standardized naming conventions:
- q — default query parameter (e.g. used by browser tab completion); should have an entity specific alias, like sku
- limit — to restrict the number of entries. See Pagination section below. Hint: You can use size as an alternate query string.
- cursor — key-based page start. See <a href="">Pagination</a> section below.
- offset — numeric offset page start. See <a href="">Pagination</a> section below. Hint: In combination with limit, you can use page as an alternative to offset.
- sort — comma-separated list of fields to sort. To indicate sorting direction, fields may be prefixed with + (ascending) or - (descending, default), e.g. /sales-orders?sort=+id
- fields — to retrieve a subset of fields. See <a href="">Support Filtering of Resource Fields</a> below.
- embed — to expand embedded entities (ie.: inside of an article entity, expand silhouette code into the silhouette object). Implementing “expand” correctly is difficult, so do it with care.

### Resources
#### MUST: Avoid Actions — Think About Resources
REST is all about your resources, so consider the domain entities that take part in web service interaction, and aim to model your API around these using the standard HTTP methods as operation indicators. For instance, if an application has to lock articles explicitly so that only one user may edit them, create an article lock with PUT or POST instead of using a lock action.
Request:
  PUT /article-locks/{article-id}
The added benefit is that you already have a service for browsing and filtering article locks.

#### Should: Model complete business processes
An API should contain the complete business processes containing all resources representing the process. This enables clients to understand the business process, foster a consistent design of the business process, allow for synergies from description and implementation perspective, and eliminates implicit invisible dependencies between APIs.
In addition, it prevents services from being designed as thin wrappers around databases, which normally tends to shift business logic to the clients.

#### Should: Define useful resources
As a rule of thumb resources should be defined to cover 90% of all its client’s use cases. A useful resource should contain as much information as necessary, but as little as possible. A great way to support the last 10% is to allow clients to specify their needs for more/less information by supporting filtering and embedding.

#### MUST: Keep URLs Verb-Free
The API describes resources, so the only place where actions should appear is in the HTTP methods. In URLs, use only nouns. Instead of thinking of actions (verbs), it’s often helpful to think about putting a message in a letter box: e.g., instead of having the verb cancel in the url, think of sending a message to cancel an order to the cancellations letter box on the server side.

#### Should: Use Domain-Specific Resource Names
API resources represent elements of the application’s domain model. Using domain-specific nomenclature for resource names helps developers to understand the functionality and basic semantics of your resources. It also reduces the need for further documentation outside the API definition. For example, “sales-order-items” is superior to “order-items” in that it clearly indicates which business object it represents. Along these lines, “items” is too general.

#### Should: Identify resources and Sub-Resources via Path Segments
Some API resources may contain or reference sub-resources. Embedded sub-resources, which are not top-level resources, are parts of a higher-level resource and cannot be used outside of its scope. Sub-resources should be referenced by their name and identifier in the path segments.
Composite identifiers must not contain / as a separator. In order to improve the consumer experience, you should aim for intuitively understandable URLs, where each sub-path is a valid reference to a resource or a set of resources. For example, if/customers/12ev123bv12v/addresses/DE_100100101 is a valid path of your API, then /customers/12ev123bv12v/addresses, /customers/12ev123bv12v and /customers must be valid as well in principle.
Basic URL structure:
  /{resources}/[resource-id]/{sub-resources}/[sub-resource-id]
  /{resources}/[partial-id-1][separator][partial-id-2]
Examples:
  /carts/1681e6b88ec1/items
  /carts/1681e6b88ec1/items/1
  /customers/12ev123bv12v/addresses/DE_100100101

#### Should: Only Use UUIDs If Necessary
Generating IDs can be a scaling problem in high frequency and near real time use cases. UUIDs solve this problem, as they can be generated without collisions in a distributed, non-coordinated way and without additional server roundtrips.
However, they also come with some disadvantages:
- pure technical key without meaning; not ready for naming or name scope conventions that might be helpful for pragmatic reasons, e.g. we learned to use names for product attributes, instead of UUIDs
- less usable, because…
- cannot be memorized and easily communicated by humans
- harder to use in debugging and logging analysis
- less convenient for consumer facing usage
- quite long: readable representation requires 36 characters and comes with higher memory and bandwidth consumption
- not ordered along their creation history and no indication of used id volume
- may be in conflict with additional backward compatibility support of legacy ids
UUIDs should be avoided when not needed for large scale id generation. Instead, for instance, server side support with id generation can be preferred (POST on id resource, followed by idempotent PUT on entity resource). Usage of UUIDs is especially discouraged as primary keys of master and configuration data, like brand-ids or attribute-ids which have low id volume but widespread steering functionality.
In any case, we should always use string rather than number type for identifiers. This gives us more flexibility to evolve the identifier naming scheme. Accordingly, if used as identifiers, UUIDs should not be qualified using a format property.
Hint: Usually, random UUID is used - see UUID version 4 in <a href="https://tools.ietf.org/html/rfc4122">RFC 4122</a>. Though UUID version 1 also contains leading timestamps it is not reflected by its lexicographic sorting. This deficit is addressed by <a href="https://github.com/alizain/ulid">ULID</a> (Universally Unique Lexicographically Sortable Identifier). You may favour ULID instead of UUID, for instance, for pagination use cases ordered along creation time.

#### May: Consider Using (Non-) Nested URLs
If a sub-resource is only accessible via its parent resource and may not exists without parent resource, consider using a nested URL structure, for instance:
  /carts/1681e6b88ec1/cart-items/1
However, if the resource can be accessed directly via its unique id, then the API should expose it as a top-level resource. For example, customer has a collection for sales orders; however, sales orders have globally unique id and some services may choose to access the orders directly, for instance:
  /customers/1681e6b88ec1
  /sales-orders/5273gh3k525a

#### Should: Limit number of Resource types
To keep maintenance and service evolution manageable, we should follow "functional segmentation" and "separation of concern" design principles and do not mix different business functionalities in same API definition. In practice this means that the number of resource types exposed via an API should be limited. In this context a resource type is defined as a set of highly related resources such as a collection, its members and any direct sub-resources.
For example, the resources below would be counted as three resource types, one for customers, one for the addresses, and one for the customers' related addresses:
  /customers
  /customers/{id}
  /customers/{id}/preferences
  /customers/{id}/addresses
  /customers/{id}/addresses/{addr}
  /addresses
  /addresses/{addr}
Note that:
- We consider /customers/{id}/preferences part of the /customers resource type because it has a one-to-one relation to the customer without an additional identifier.
- We consider /customers and /customers/{id}/addresses as separate resource types because /customers/{id}/addresses/{addr} also exists with an additional identifier for the address.
- We consider /addresses and /customers/{id}/addresses as separate resource types because there’s no reliable way to be sure they are the same.
Given this definition, our experience is that well defined APIs involve no more than 4 to 8 resource types. There may be exceptions with more complex business domains that require more resources, but you should first check if you can split them into separate subdomains with distinct APIs.
Nevertheless one API should hold all necessary resources to model complete business processes helping clients to understand these flows.

#### Should: Limit number of Sub-Resource Levels
There are main resources (with root url paths) and sub-resources (or “nested” resources with non-root urls paths). Use sub-resources if their life cycle is (loosely) coupled to the main resource, i.e. the main resource works as collection resource of the subresource entities. You should use less than 4 sub-resource (nesting) levels — more levels increase API complexity and url path length. (Remember, some popular web browsers do not support URLs of more than 2000 characters)

### HTTP
#### MUST: Use HTTP Methods Correctly
Be compliant with the standardized HTTP method semantics summarized as follows:
##### GET
GET requests are used to read a single resource or query set of resources.
- GET requests for individual resources will usually generate a 404 if the resource does not exist
- GET requests for collection resources may return either 200 (if the listing is empty) or 404 (if the list is missing)
- GET requests must NOT have request body payload
Note: GET requests on collection resources should provide a sufficient filter mechanism as well as <a href="">Pagination</a>.

##### "GET with Body"
APIs sometimes face the problem, that they have to provide extensive structured request information with GET, that may even conflicts with the size limits of clients, load-balancers, and servers. As we require APIs to be standard conform (body in GET must be ignored on server side), API designers have to check the following two options:
1. GET with URL encoded query parameters: when it is possible to encode the request information in query parameters, respecting the usual size limits of clients, gateways, and servers, this should be the first choice. The request information can either be provided distributed to multiple query parameters or a single structured URL encoded string.
2. POST with body content: when a GET with URL encoded query parameters is not possible, a POST with body content must be used. In this case the endpoint must be documented with the hint GET with body to transport the GET semantic of this call.
Note: It is no option to encode the lengthy structured request information in header parameters. From a conceptual point of view, the semantic of an operation should always be expressed by resource name and query parameters, i.e. what goes into the URL. Request headers are reserved for general context information, e.g. FlowIDs. In addition, size limits on query parameters and headers are not reliable and depend on clients, gateways, server, and actual settings. Thus, switching to headers does not solve the original problem.

##### PUT
PUT requests are used to create or update entire resources - single or collection resources. The semantic is best described as »please put the enclosed representation at the resource mentioned by the URL, replacing any existing resource.«.
- PUT requests are usually applied to single resources, and not to collection resources, as this would imply replacing the entire collection
- PUT requests are usually robust against non-existence of resources by implicitly creating before updating
- on successful PUT requests, the server will replace the entire resource addressed by the URL with the representation passed in the payload (subsequent reads will deliver the same payload)
- successful PUT requests will usually generate 200 or 204 (if the resource was updated - with or without actual content returned), and 201 (if the resource was created)
<b>Note</b>: Resource IDs with respect to PUT requests are maintained by the client and passed as a URL path segment. Putting the same resource twice is required to be idempotent and to result in the same single resource instance. If PUT is applied for creating a resource, only URIs should be allowed as resource IDs. If URIs are not available <b>POST</b> should be preferred.
To prevent unnoticed concurrent updates when using PUT, the combination of ETag and If-(None-)Match headers should be considered to signal the server stricter demands to expose conflicts and prevent lost updates. The section “Optimistic Locking in RESTful APIs” also describes some alternatives to this approach.

##### POST
POST requests are idiomatically used to create single resources on a collection resource endpoint, but other semantics on single resources endpoint are equally possible. The semantic for collection endpoints is best described as »please add the enclosed representation to the collection resource identified by the URL«. The semantic for single resource endpoints is best described as »please execute the given well specified request on the collection resource identified by the URL«.
- POST request should only be applied to collection resources, and normally not on single resource, as this has an undefined semantic
- on successful POST requests, the server will create one or multiple new resources and provide their URI/URLs in the response
- successful POST requests will usually generate 200 (if resources have been updated), 201 (if resources have been created), and 202 (if the request was accepted but has not been finished yet)
<b>More generally</b>: POST should be used for scenarios that cannot be covered by the other methods sufficiently. For instance, GET with complex (e.g. SQL like structured) query that needs to be passed as request body payload because of the URL-length constraint. In such cases, make sure to document the fact that POST is used as a workaround.
<b>Note</b>: Resource IDs with respect to POST requests are created and maintained by server and returned with response payload. Posting the same resource twice is by itself not required to be idempotent and may result in multiple resource instances. Anyhow, if external URIs are present that can be used to identify duplicate requests, it is best practice to implement POST in an idempotent way.

##### PATCH
PATCH request are only used for partial update of single resources, i.e. where only a specific subset of resource fields should be replaced. The semantic is best described as »please change the resource identified by the URL according to my change request«. The semantic of the change request is not defined in the HTTP standard and must be described in the API specification by using suitable media types.
- PATCH requests are usually applied to single resources, and not on collection resources, as this would imply patching on the entire collection
- PATCH requests are usually not robust against non-existence of resource instances
- on successful PATCH requests, the server will update parts of the resource addressed by the URL as defined by the change request in the payload
- successful PATCH requests will usually generate 200 or 204 (if resources have been updated, with or without updated content returned)
Note: since implementing PATCH correctly is a bit tricky, we strongly suggest to choose one and only one of the following patterns per endpoint, unless forced by a backwards compatible change. In preference order:
1. use PUT with complete objects to update a resource as long as feasible (i.e. do not use PATCH at all).
2. use PATCH with partial objects to only update parts of a resource, whenever possible. (This is basically <a href="https://tools.ietf.org/html/rfc7396">JSON Merge Patch</a>, a specialized media type application/merge-patch+json that is a partial resource representation.)
3. use PATCH with <a href="http://tools.ietf.org/html/rfc6902">JSON Patch</a>, a specialized media type application/json-patch+json that includes instructions on how to change the resource.
4. use POST (with a proper description of what is happening) instead of PATCH if the request does not modify the resource in a way defined by the semantics of the media type.
In practice <a href="https://tools.ietf.org/html/rfc7396">JSON Merge Patch</a> quickly turns out to be too limited, especially when trying to update single objects in large collections (as part of the resource). In this cases <a href="http://tools.ietf.org/html/rfc6902">JSON Patch</a> can shown its full power while still showing readable patch requests (see also <a href="http://erosb.github.io/post/json-patch-vs-merge-patch">JSON patch vs. merge</a>).
To prevent unnoticed concurrent updates when using PATCH, the combination of ETag`and `If-Match headers should be considered to signal the server stricter demands to expose conflicts and prevent lost updates.

##### DELETE
DELETE request are used to delete resources. The semantic is best described as »please delete the resource identified by the URL«.
- DELETE requests are usually applied to single resources, not on collection resources, as this would imply deleting the entire collection
- successful DELETE request will usually generate 200 (if the deleted resource is returned) or 204 (if no content is returned)
- failed DELETE request will usually generate 404 (if the resource cannot be found) or 410 (if the resource was already deleted before)

##### HEAD
HEAD requests are used to retrieve the header information of single resources and resource collections.
- HEAD has exactly the same semantics as GET, but returns headers only, no body.

##### OPTIONS
OPTIONS are used to inspect the available operations (HTTP methods) of a given endpoint.
- OPTIONS requests usually either return a comma separated list of methods (provided by an Allow:-Header) or as a structured list of link templates
<b>Note</b>: OPTIONS is rarely implemented, though it could be used to self-describe the full functionality of a resource.

#### Should: Fulfill Safeness and Idempotency Properties
An operation can be…
- idempotent, i.e. operation will have the same effect on the server’s state if executed once or multiple times (note: this does not necessarily mean returning the same response or status code)
- safe, i.e. must not have side effects such as state changes
Method implementations must fulfill the following basic properties:
HTTP method   safe  idempotent
OPTIONS       Yes	  Yes
HEAD	        Yes	  Yes
GET	          Yes	  Yes
PUT	          No	  Yes
POST	        No	  No
DELETE	      No	  Yes
PATCH	        No	  No

#### Should: Use Specific HTTP Status Codes
This guideline groups the following rules for HTTP status codes usage:
•	You must not invent new HTTP status codes; only use standardized HTTP status codes and consistent with its intended semantics.
•	You should use the most specific HTTP status code for your concrete resource request processing status or error situation.
•	When using HTTP status codes that are less commonly used and not listed below, you must provide good documentation in the API definition.
There are ~60 different HTTP status codes with specific semantics defined in the HTTP standards (mainly <a href="https://tools.ietf.org/html/rfc7231#section-6">RFC7231</a> and <a href="https://tools.ietf.org/html/rfc6585">RFC-6585</a>) - and there are upcoming new ones, e.g. <a href="https://tools.ietf.org/html/draft-tbray-http-legally-restricted-status-05">draft legally-restricted-status</a> (see overview on all error codes on <a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes">Wikipedia</a> or via <a href="https://httpstatuses.com/">https://httpstatuses.com/</a>). And there are unofficial ones, e.g. used by specific web servers like Nginx.

TODO

#### Should: Specify Success and Error Responses
APIs should define the functional, business view and abstract from implementation aspects. Success and error responses are a vital part to define how an API is used correctly.
Therefore, the OpenAPI specification must contain definitions for ALL success and error responses. Both are part of the interface definition and provide important information for service clients to handle standard as well as exceptional situations. For each response the most specific HTTP status code should be selected that conveys the domain-specific success or error semantic — see “Use Specific HTTP Status Codes”.
To transport error information beside the status code, we should use a standardized error return object specification. 

Specification is yet to be defined !

Note: It is best practice to explicitly specify all successful status codes. This prevents any conflict with the above pattern.
The list of status code that in general can be omitted from API specifications includes but is not limited to:
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found unless it has some additional semantics
- 405 Method Not Allowed
- 406 Not Acceptable
- 408 Request Timeout
- 413 Payload Too Large
- 414 URI Too Long
- 415 Unsupported Media Type
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout

Note: Even though all status code may be documented explicitly, clients must always be prepared for responses with unexpected status codes. In case of doubt they should handle them like they would handle the corresponding x00 code. Adding new response codes (specially error responses) should be considered a compatible API evolution.

API designers should also think about a troubleshooting board as part of the associated online API documentation. It provides information and handling guidance on application-specific errors and is referenced via links from the API specification. This can reduce service support tasks and contribute to service client and provider performance.

#### Should: Use 207 for Batch or Bulk Requests
Some APIs are required to provide either batch or bulk requests using POST for performance reasons, i.e. for communication and processing efficiency. In this case services may be in need to signal multiple response codes for each part of an batch or bulk request. As HTTP does not provide proper guidance for handling batch/bulk requests and responses, we herewith define the following approach:
- A batch or bulk request always has to respond with HTTP status code 207, unless it encounters a generic or unexpected failure before looking at individual parts.
- A batch or bulk response with status code 207 always returns a multi-status object containing sufficient status and/or monitoring information for each part of the batch or bulk request.
- A batch or bulk request may result in a status code 400/500, only if the service encounters a failure before looking at individual parts or, if an unanticipated failure occurs.
The before rules apply even in the case that processing of all individual part fail or each part is executed asynchronously! They are intended to allow clients to act on batch and bulk responses by inspecting the individual results in a consistent way.
<b>Note</b>: while a batch defines a collection of requests triggering independent processes, a bulk defines a collection of independent resources created or updated together in one request. With respect to response processing this distinction normally does not matter.

#### Should: Use 429 with Headers for Rate Limits
APIs that wish to manage the request rate of clients must use the '<a href="http://tools.ietf.org/html/rfc6585">429 Too Many Requests</a>' response code if the client exceeded the request rate and therefore the request can’t be fulfilled. Such responses must also contain header information providing further details to the client. There are two approaches a service can take for header information:
- Return a '<a href="https://tools.ietf.org/html/rfc7231#section-7.1.3">Retry-After</a>' header indicating how long the client ought to wait before making a follow-up request. The Retry-After header can contain a HTTP date value to retry after or the number of seconds to delay. Either is acceptable but APIs should prefer to use a delay in seconds.
- Return a trio of 'X-RateLimit' headers. These headers (described below) allow a server to express a service level in the form of a number of allowing requests within a given window of time and when the window is reset.
The 'X-RateLimit' headers are:
- X-RateLimit-Limit: The maximum number of requests that the client is allowed to make in this window.
- X-RateLimit-Remaining: The number of requests allowed in the current window.
- X-RateLimit-Reset: The relative time in seconds when the rate limit window will be reset.
The reason to allow both approaches is that APIs can have different needs. Retry-After is often sufficient for general load handling and request throttling scenarios and notably, does not strictly require the concept of a calling entity such as a tenant or named account. In turn this allows resource owners to minimise the amount of state they have to carry with respect to client requests. The 'X-RateLimit' headers are suitable for scenarios where clients are associated with pre-existing account or tenancy structures. 'X-RateLimit' headers are generally returned on every request and not just on a 429, which implies the service implementing the API is carrying sufficient state to track the number of requests made within a given window for each named entity.

#### Should: Explicitly define the Collection Format of Query Parameters
There are different ways of supplying a set of values as a query parameter. One particular type should be selected and stated explicitly in the API definition. The OpenAPI property collectionFormat is used to specify the format of the query parameter.
Only the csv or multi formats should be used for multi-value query parameters as described below.
- Comma separated values (csv)
  ?parameter=value1,value2,value3
- Multiple parameter instances	(multi)
  ?parameter=value1&parameter=value2&parameter=value3
When choosing the collection format, take into account the tool support, the escaping of special characters and the maximal URL length.

### Performance
#### Should: Reduce Bandwidth Needs and Improve Responsiveness
APIs should support techniques for reducing bandwidth based on client needs. This holds for APIs that (might) have high payloads and/or are used in high-traffic scenarios like the public Internet and telecommunication networks. Typical examples are APIs used by mobile web app clients with (often) less bandwidth connectivity. 
Common techniques include:
- gzip compression
- querying field filters to retrieve a subset of resource attributes 
- paginate lists of data items (see “Pagination” below)
- ETag and If-(None-)Match headers to avoid re-fetching of unchanged resources. (see May:Consider using ETag together with If-(None-)Match header
- pagination for incremental access of larger (result) lists
Each of these items is described in greater detail below.

#### May: Use gzip Compression
Compress the payload of your API’s responses with gzip, unless there’s a good reason not to — for example, you are serving so many requests that the time to compress becomes a bottleneck. This helps to transport data faster over the network (fewer bytes) and makes frontends respond faster.
Though gzip compression might be the default choice for server payload, the server should also support payload without compression and its client control via Accept-Encoding request header — see also <a href="http://tools.ietf.org/html/rfc7231#section-5.3.4">RFC 7231 Section 5.3.4</a>. The server should indicate used gzip compression via the Content-Encoding header.

#### Should: Support Filtering of Resource Fields
Depending on your use case and payload size, you can significantly reduce network bandwidth need by supporting filtering of returned entity fields. Here, the client can determine the subset of fields he wants to receive via the fields query parameter:
<b>Unfiltered</b>
  GET http://api.example.org/resources/123 HTTP/1.1
  
  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": "cddd5e44-dae0-11e5-8c01-63ed66ab2da5",
    "name": "John Doe",
    "address": "1600 Pennsylvania Avenue Northwest, Washington, DC, United States",
    "birthday": "1984-09-13",
    "partner": {
      "id": "1fb43648-dae1-11e5-aa01-1fbc3abb1cd0",
      "name": "Jane Doe",
      "address": "1600 Pennsylvania Avenue Northwest, Washington, DC, United States",
      "birthday": "1988-04-07"
    }
  }
<b>Filtered</b>
  GET http://api.example.org/resources/123?fields=(name,partner(name)) HTTP/1.1
  
  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "name": "John Doe",
    "partner": {
      "name": "Jane Doe"
    }
  }
As illustrated by this example, field filtering should be done via request parameter "fields" with value range defined by the following BNF grammar.
  <fields> ::= <negation> <fields_expression> | <fields_expression>
  <negation> ::= "!"
  <fields_expression> ::= "(" <field_set> ")"
  <field_set> ::= <qualified_field> | <qualified_field> "," <field_set>
  <qualified_field> ::= <field> | <field> <fields_expression>
  <field> ::= <DASH_LETTER_DIGIT> | <DASH_LETTER_DIGIT> <field>
  <DASH_LETTER_DIGIT> ::= <DASH> | <LETTER> | <DIGIT>
  <DASH> ::= "-" | "_"
  <LETTER> ::= "A" | "B" | "C" | "D" | "E" | "F" | "G" | "H" | "I" | "J" | "K" | "L" | "M" | "N" | "O" | "P" | "Q" | "R" | "S" | "T" | "U" | "V" | "W" | "X" | "Y" | "Z" | "a" | "b" | "c" | "d" | "e" | "f" | "g" | "h" | "i" | "j" | "k" | "l" | "m" | "n" | "o" | "p" | "q" | "r" | "s" | "t" | "u" | "v" | "w" | "x" | "y" | "z"
  <DIGIT> ::= "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
A fields_expression as defined by the grammar describes the properties of an object, i.e. (name)returns only the name property of the root object. (name,partner(name)) returns the name and partner properties where partner itself is also an object and only its name property is returned.
Hint: OpenAPI doesn’t allow you to formally specify whether depending on a given parameter will return different parts of the specified result schema. Explain this in English in the parameter description.

#### Should: Allow Optional Embedding of Sub-Resources
Embedding related resources (also known as Resource expansion) is a great way to reduce the number of requests. In cases where clients know upfront that they need some related resources they can instruct the server to prefetch that data eagerly. Whether this is optimized on the server, e.g. a database join, or done in a generic way, e.g. an HTTP proxy that transparently embeds resources, is up to the implementation.
See “Use Conventional Query Strings” for naming, e.g. "embed" for steering of embedded resource expansion. Please use the <a href="https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form">BNF</a> grammar, as already defined above for filtering, when it comes to an embedding query syntax.
Embedding a sub-resource can possibly look like this where an order resource has its order items as sub-resource (/order/{orderId}/items):
  GET /order/123?embed=(items) HTTP/1.1

  {
    "id": "123",
    "_embedded": {
      "items": [
        {
          "position": 1,
          "sku": "1234-ABCD-7890",
          "price": {
            "amount": 71.99,
            "currency": "EUR"
          }
        }
      ]
    }
  }

#### Should: Document Caching, if Supported
If an API is intended to support caching, it must take care to specify this ability by defining the caching boundaries i.e. time-to-live and cache constraints, by providing the Cache-Control and Vary headers (Please read <a href="https://tools.ietf.org/html/rfc7234">RFC-7234</a> carefully).
Caching has to take many aspects into account, e.g. general cacheability of response information, our guideline to protect endpoints using SSL and OAuth authorization, resource update and invalidation rules, existence of multiple consumer instances. As a consequence, caching is in best case complex, in worst case inefficient. <b>Thus, client side and transparent HTTP caching should be avoided for RESTful APIs unless the API designer has proven to know better.</b>
As default, API providers should set the Cache-Control: no-cache header.
Note: There is no need to document this default setting, that is attached to each response automatically by most frameworks. However, any use deviating from this default must be sufficiently documented.

### Pagination
#### Should: Support Pagination
Access to lists of data items must support pagination for best client side batch processing and iteration experience. This holds true for all lists that are (potentially) larger than just a few hundred entries.
There are two page iteration techniques:
- Offset/Limit-based pagination: numeric offset identifies the first page entry
- Cursor-based — aka key-based — pagination: a unique key element identifies the first page entry (see also <a href="https://developers.facebook.com/docs/graph-api/using-graph-api/v2.4#paging">Facebook’s guide</a>)
The technical conception of pagination should also consider user experience related issues. As mentioned in this <a href="https://www.smashingmagazine.com/2016/03/pagination-infinite-scrolling-load-more-buttons/">article</a>, jumping to a specific page is far less used than navigation via next/previous page links. This favours cursor-based over offset-based pagination.

#### Should: Prefer Cursor-Based Pagination, Avoid Offset-Based Pagination
Cursor-based pagination is usually better and more efficient when compared to offset-based pagination. Especially when it comes to high-data volumes and / or storage in NoSQL databases.
Before choosing cursor-based pagination, consider the following trade-offs:
- Usability/framework support:
  - Offset / limit based pagination is more known than cursor-based pagination, so it has more framework support and is easier to use for API clients
- Use case: Jump to a certain page
  - If jumping to a particular page in a range (e.g., 51 of 100) is really a required use case, cursor-based navigation is not feasible
- Variability of data may lead to anomalies in result pages
  - Offset-based pagination may create duplicates or lead to missing entries if rows are inserted or deleted between two subsequent paging requests.
  - When using cursor-based pagination, paging cannot continue when the cursor entry has been deleted while fetching two pages
- Performance considerations - efficient server-side processing using offset-based pagination is hardly feasible for:
  - Higher data list volumes, especially if they do not reside in the database’s main memory
  - Sharded or NoSQL databases
  - Cursor-based navigation may not work if you need the total count of results and / or backward iteration support

Further reading:
- <a href="https://dev.twitter.com/rest/public/timelines">Twitter</a>
- <a href="http://use-the-index-luke.com/no-offset">Use the Index, Luke</a>
- <a href="https://www.citusdata.com/blog/1872-joe-nelson/409-five-ways-paginate-postgres-basic-exotic">Paging in PostgreSQL</a>

#### May: Use Pagination Links Where Applicable
API implementing <a href="">HATEOS</a> may use <a href="">simplified hypertext controls</a> for pagination within collections.
Those collections should then have an items attribute holding the items of the current page. The collection may contain additional metadata about the collection or the current page (e.g. index,page_size) when necessary.
You should avoid providing a total count in your API unless there’s a clear need to do so. Very often, there are systems and performance implications to supporting full counts, especially as datasets grow and requests become complex queries or filters that drive full scans (e.g., your database might need to look at all candidate items to count them). While this is an implementation detail relative to the API, it’s important to consider your ability to support serving counts over the life of a service.
If the collection consists of links to other resources, the collection name should use <a href="http://www.iana.org/assignments/link-relations/link-relations.xml">IANA registered link relations</a> as names whenever appropriate, but use plural form.
E.g. a service for articles could represent the collection of hyperlinks to an article’s authors like that:
  {
    "self": "https://.../articles/xyz/authors/",
    "index": 0,
    "page_size": 5,
    "items": [
      {
        "href": "https://...",
        "id": "123e4567-e89b-12d3-a456-426655440000",
        "name": "Kent Beck"
      },
      {
        "href": "https://...",
        "id": "987e2343-e89b-12d3-a456-426655440000",
        "name": "Mike Beedle"
      },
      ...
    ],
    "first": "https://...",
    "next": "https://...",
    "prev": "https://...",
    "last": "https://..."
  }

### Hypermedia
#### Must: Use REST Maturity Level 2
We strive for a good implementation of <a href="http://martinfowler.com/articles/richardsonMaturityModel.html#level2">REST Maturity Level 2</a> as it enables us to build resource-oriented APIs that make full use of HTTP verbs and status codes. You can see this expressed by many rules throughout these guidelines.
- Avoid Actions — Think About Resources
- Keep URLs Verb-Free
- Use HTTP Methods Correctly
- Use Specific HTTP Status Codes
Although this is not HATEOAS, it should not prevent you from designing proper link relationships in your APIs as stated in rules below.

#### May: Use REST Maturity Level 3 - HATEOAS
We do not generally recommend to implement <a href="http://martinfowler.com/articles/richardsonMaturityModel.html#level3">REST Maturity Level 3</a>. HATEOAS comes with additional API complexity without real value in our SOA context where client and server interact via REST APIs and provide complex business functions as part of our e-commerce SaaS platform.
Our major concerns regarding the promised advantages of HATEOAS (see also <a href="https://www.infoq.com/news/2014/03/rest-at-odds-with-web-apis for detailed discussion">RESTistential Crisis over Hypermedia APIs</a>, <a href="https://jeffknupp.com/blog/2014/06/03/why-i-hate-hateoas/">Why I Hate HATEOAS</a> and others):
- We follow the <a href="./Guidelines.md#must-follow-api-first-principle">API First principle</a> with APIs explicitly defined outside the code with standard specification language. HATEOAS does not really add value for SOA client engineers in terms of API self-descriptiveness: a client engineer finds necessary links and usage description (depending on resource state) in the API reference definition anyway.
- Generic HATEOAS clients which need no prior knowledge about APIs and explore API capabilities based on hypermedia information provided, is a theoretical concept that we haven’t seen working in practice and does not fit to our SOA set-up. The OpenAPI description format (and tooling based on OpenAPI) doesn’t provide sufficient support for HATEOAS either.
- In practice relevant HATEOAS approximations (e.g. following specifications like HAL or JSON API) support API navigation by abstracting from URL endpoint and HTTP method aspects via link types. So, Hypermedia does not prevent clients from required manual changes when domain model changes over time.
- Hypermedia make sense for humans, less for SOA machine clients. We would expect use cases where it may provide value more likely in the frontend and human facing service domain.
- Hypermedia does not prevent API clients to implement shortcuts and directly target resources without 'discovering' them.
However, we do not forbid HATEOAS; you could use it, if you checked its limitations and still see clear value for your usage scenario that justifies its additional complexity. If you use HATEOAS please share experience.

#### Should: Use full, absolute URI
Links to other resource must always use full, absolute URI.
<b>Motivation</b>: Exposing any form of relative URI (no matter if the relative URI uses an absolute or relative path) introduces avoidable client side complexity. It also requires clarity on the base URI, which might not be given when using features like embedding subresources. The primary advantage of non-absolute URI is reduction of the payload size, which is better achievable by following the recommendation to use gzip compression.

#### Should: Use Common Hypertext Controls
When embedding links to other resources into representations you must use the common hypertext control object. It contains at least one attribute:
- href: The URI of the resource the hypertext control is linking to. All our API are using HTTPS as URI scheme.
In API that contain any hypertext controls, the attribute name href is reserved for usage within hypertext controls.
The schema for hypertext controls can be derived from this model:
  HttpLink:
    description: A base type of objects representing links to resources.
    type: object
    properties:
      href:
        description: Any URI that is using http or https protocol
        type: string
        format: uri
    required: [ "href" ]
The name of an attribute holding such a HttpLink object specifies the relation between the object that contains the link and the linked resource. Implementations should use names from the IANA Link Relation Registry whenever appropriate. As IANA link relation names use hyphen-case notation, while this guide enforces snake_case notation for attribute names, hyphens in IANA names have to be replaced with underscores (e.g. the IANA link relation type version-history would become the attribute version_history)
Specific link objects may extend the basic link type with additional attributes, to give additional information related to the linked resource or the relationship between the source resource and the linked one.
E.g. a service providing "Person" resources could model a person who is married with some other person with a hypertext control that contains attributes which describe the other person (id, name) but also the relationship "spouse" between the two persons (since):
  {
    "id": "446f9876-e89b-12d3-a456-426655440000",
    "name": "Peter Mustermann",
    "spouse": {
      "href": "https://...",
      "since": "1996-12-19",
      "id": "123e4567-e89b-12d3-a456-426655440000",
      "name": "Linda Mustermann"
    }
  }
Hypertext controls are allowed anywhere within a JSON model. While this specification would allow <a href="http://stateless.co/hal_specification.html">HAL</a>, we actually don’t recommend/enforce the usage of HAL anymore as the structural separation of meta-data and data creates more harm than value to the understandability and usability of an API.

#### Should: Use Simple Hypertext Controls for Pagination and Self-References
Hypertext controls for pagination inside collections and self-references should use a simple URI value in combination with their corresponding <a href="http://www.iana.org/assignments/link-relations/link-relations.xml">link relations</a> (next, prev, first, last, self) instead of the extensible common hypertext control
See <a href="">Pagination</a> for information how to best represent paginateable collections.

#### Should: Not Use “Link Headers” with JSON entities
We don’t allow the use of the Link-entity <a href="http://tools.ietf.org/html/rfc5988#section-5">Header defined by RFC 5988</a> in conjunction with JSON media types. We prefer links directly embedded in JSON payloads to the uncommon link header syntax.

### Data formats
#### Should: Use JSON to Encode Structured Data
Use JSON-encoded body payload for transferring structured data. The JSON payload must follow <a href="https://tools.ietf.org/html/rfc7159">RFC-7159</a> by having (if possible) a serialized object as the top-level structure, since it would allow for future extension. This also applies for collection resources where one naturally would assume an array. 

#### May: Use non JSON Media Types for Binary Data or Alternative Content Representations
Other media types may be used in following cases:
- Transferring binary data or data whose structure is not relevant. This is the case if payload structure is not interpreted and consumed by clients as is. Example of such use case is downloading images in formats JPG, PNG, GIF.
- In addition to JSON version alternative data representations (e.g. in formats PDF, DOC, XML) may be made available through content negotiation.

#### Should: Use Standard Date and Time Formats
##### JSON Payload
Read more about date and time format in Date property values should conform to <a href="https://tools.ietf.org/html/rfc3339">RFC 3339</a>.
##### HTTP headers
Http headers including the proprietary headers use the <a href="http://tools.ietf.org/html/rfc7231#section-7.1.1.1">HTTP date format defined in RFC 7231</a>.

#### Should: Use Standards for Country, Language and Currency Codes
Use the following standard formats for country, language and currency codes:
- ISO 3166-1-alpha2 country codes
- ISO 639-1 language code
- ISO 4217 currency codes

#### Should: Define Format for Type Number and Integer
Whenever an API defines a property of type number or integer, the precision must be defined by the format as follows to prevent clients from guessing the precision incorrectly, and thereby changing the value unintentionally:
  integer - int32 - integer between -2x31 and 2x31-1
  integer - int64 - integer between -2x63 and 2x63-1
  integer - bigint - arbitrarily large signed integer number
  number - float - IEEE 754-2008/ISO 60559:2011 binary64 decimal number
  number - double - IEEE 754-2008/ISO 60559:2011 binary128 decimal number
  number - decimal - arbitrarily precise signed decimal number
The precision must be translated by clients and servers into the most specific language types. E.g. for the following definitions the most specific language types in Java will translate to BigDecimal forMoney.amount and int or Integer for the OrderList.page_size:
  Money:
    type: object
    properties:
      amount:
        type: number
        description: Amount expressed as a decimal number of major currency units
        format: decimal
        example: 99.95
     ...

  OrderList:
    type: object
    properties:
      page_size:
        type: integer
        description: Number of orders in list
        format: int32
        example: 42

#### Should: Prefer standard Media type name application/json
Previously, this guideline allowed the use of custom media types. This usage is not recommended anymore and should be avoided, except where it is necessary for cases of media type versioning. Instead, the standard media type name application/json (or Problem JSON) should be used.
Custom media types with subtypes beginning with x bring no advantage compared to the standard media type for JSON, and make automated processing more difficult. They are also <a href="https://tools.ietf.org/html/rfc6838#section-3.4">discouraged by RFC 6838</a>.

### Common Data Types
Definitions of data objects that are good candidates for wider usage:

#### Should: Use a Common Money Object
Use the following common money structure:
  Money:
    type: object
    properties:
      amount:
        type: number
        description: Amount expressed as a decimal number of major currency units
        format: decimal
        example: 99.95
      currency:
        type: string
        description: 3 letter currency code as defined by ISO-4217
        format: iso-4217
        example: EUR
    required:
      - amount
      - currency
The decimal values for "amount" describe unit and subunit of the currency in a single value, where the digits before the decimal point are for the major unit and the digits after the decimal point are for the minor unit. Note that some business cases (e.g. transactions in Bitcoin) call for a higher precision, so applications must be prepared to accept values with unlimited precision, unless explicitly stated otherwise in the API specification. Examples for correct representations (in EUR):
- 42.20 or 42.2 = 42 Euros, 20 Cent
- 0.23 = 23 Cent
- 42.0 or 42 = 42 Euros
- 1024.42 = 1024 Euros, 42 Cent
- 1024.4225 = 1024 Euros, 42.25 Cent
Make sure that you don’t convert the “amount” field to float / double types when implementing this interface in a specific language or when doing calculations. Otherwise, you might lose precision. Instead, use exact formats like Java’s BigDecimal. See <a href="http://stackoverflow.com/a/3730040/342852">Stack Overflow</a> for more info.
<b>Some JSON parsers (NodeJS’s, for example) convert numbers to floats by default. After discussing the pros and cons, we’ve decided on "decimal" as our amount format. It is not a standard OpenAPI format, but should help us to avoid parsing numbers as float / doubles.</b>

#### Should: Use common field names and semantics
There exist a variety of field types that are required in multiple places. To achieve consistency across all API implementations, you must use common field names and semantics whenever applicable.
##### Generic Fields
There are some data fields that come up again and again in API data:
- id: the identity of the object. If used, IDs must be opaque strings and not numbers. IDs are unique within some documented context, are stable and don’t change for a given object once assigned, and are never recycled cross entities.
- xyz_id: an attribute within one object holding the identifier of another object must use a name that corresponds to the type of the referenced object or the relationship to the referenced object followed by _id (e.g. customer_id not customer_number; parent_node_id for the reference to a parent node from a child node, even if both have the type Node)
- created: when the object was created. If used, this must be a date-time construct.
- modified: when the object was updated. If used, this must be a date-time construct.
- type: the kind of thing this object is. If used, the type of this field should be a string. Types allow runtime information on the entity provided that otherwise requires examining the Open API file.
Example JSON schema:
  tree_node:
    type: object
    properties:
      id:
        description: the identifier of this node
        type: string
      created:
        description: when got this node created
        type: string
        format: 'date-time'
      modified:
        description: when got this node last updated
        type: string
        format: 'date-time'
      type:
        type: string
        enum: [ 'LEAF', 'NODE' ]
      parent_node_id:
        description: the identifier of the parent node of this node
        type: string
    example:
      id: '123435'
      created: '2017-04-12T23:20:50.52Z'
      modified: '2017-04-12T23:20:50.52Z'
      type: 'LEAF'
      parent_node_id: '534321'
These properties are not always strictly necessary, but making them idiomatic allows API client developers to build up a common understanding of the resources. There is very little utility for API consumers in having different names or value types for these fields across APIs.
##### Address Fields
Address structures play a role in different functional and use-case contexts, including country variances. All attributes that relate to address information should follow the naming and semantics defined below.
  addressee:
    description: a (natural or legal) person that gets addressed
    type: object
    required:
      - first_name
      - last_name
      - street
      - city
      - zip
      - country_code
    properties:
      salutation:
        description: |
          a salutation and/or title used for personal contacts to some
          addressee; not to be confused with the gender information!
        type: string
        example: Mr
      first_name:
        description: |
          given name(s) or first name(s) of a person; may also include the
          middle names.
        type: string
        example: Hans Dieter
      last_name:
        description: |
          family name(s) or surname(s) of a person
        type: string
        example: Mustermann
      business_name:
        description: |
          company name of the business organization. Used when a business is
          the actual addressee; for personal shipments to office addresses, use
          `care_of` instead.
        type: string
        example: Consulting Services GmbH
    required:
      - first_name
      - last_name

  address:
    description:
      an address of a location/destination
    type: object
    properties:
      care_of:
        description: |
          (aka c/o) the person that resides at the address, if different from
          addressee. E.g. used when sending a personal parcel to the
          office /someone else's home where the addressee resides temporarily
        type: string
        example: Consulting Services GmbH
      street:
        description: |
          the full street address including house number and street name
        type: string
        example: Schönhauser Allee 103
      additional:
        description: |
          further details like building name, suite, apartment number, etc.
        type: string
        example: 2. Hinterhof rechts
      city:
        description: |
          name of the city / locality
        type: string
        example: Berlin
      zip:
        description: |
          zip code or postal code
        type: string
        example: 14265
      country_code:
        description: |
          the country code according to
          [iso-3166-1-alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)
        type: string
        example: DE
    required:
      - street
      - city
      - zip
      - country_code
Grouping and cardinality of fields in specific data types may vary based on the specific use case (e.g. combining addressee and address fields into a single type when modeling an address label vs distinct addressee and address types when modeling users and their addresses).

#### Should: Follow Hypertext Control Conventions
APIs that provide hypertext controls (links) to interconnect API resources must follow the conventions for naming and modeling of hypertext controls as defined in section <a href="">Hypermedia</a>.

#### Should: Use Problem JSON
<a href="http://tools.ietf.org/html/rfc7807">RFC 7807</a> defines the media type application/problem+json. Operations should return that (together with a suitable status code) when any problem occurred during processing and you can give more details than the status code itself can supply, whether it be caused by the client or the server (i.e. both for 4xx or 5xx errors).
A previous version of this guideline (before the publication of that RFC and the registration of the media type) told to return application/x.problem+json in these cases (with the same contents). Servers for APIs defined before this change should pay attention to the Accept header sent by the client and set the Content-Type header of the problem response correspondingly. Clients of such APIs should accept both media types.
APIs may define custom problems types with extension properties, according to their specific needs.

#### MUST: Do not expose Stack Traces
Stack traces contain implementation details that are not part of an API, and on which clients should never rely. Moreover, stack traces can leak sensitive information that partners and third parties are not allowed to receive and may disclose insights about vulnerabilities to attackers.

### Common Headers
This section describes a handful of headers, which we found raised the most questions in our daily usage, or which are useful in particular circumstances but not widely known.

#### Should: Use Content Headers Correctly
Content or entity headers are headers with a Content- prefix. They describe the content of the body of the message and they can be used in both, HTTP requests and responses. Commonly used content headers include but are not limited to:
- Content-Disposition can indicate that the representation is supposed to be saved as a file, and the proposed file name.
- Content-Encoding indicates compression or encryption algorithms applied to the content.
- Content-Length indicates the length of the content (in bytes).
- Content-Language indicates that the body is meant for people literate in some human language(s).
- Content-Location indicates where the body can be found otherwise (See “Use Content-Location Header” for more details).
- Content-Range is used in responses to range requests to indicate which part of the requested resource representation is delivered with the body.
- Content-Type indicates the media type of the body content.

#### Should: Use Standardized Headers
Use <a href="http://en.wikipedia.org/wiki/List_of_HTTP_header_fields">this list</a> and mention its support in your OpenAPI definition.

#### May: Use Content-Location Header
The Content-Location header is optional and can be used in successful write operations (PUT, POST or PATCH) or read operations (GET, HEAD) to guide caching and signal a receiver the actual location of the resource transmitted in the response body. This allows clients to identify the resource and to update their local copy when receiving a response with this header.
The Content-Location header can be used to support the following use cases:
- For reading operations GET and HEAD, a different location than the requested URI can be used to indicate that the returned resource is subject to content negotiations, and that the value provides a more specific identifier of the resource.
- For writing operations PUT and PATCH, an identical location to the requested URI, can be used to explicitly indicate that the returned resource is the current representation of the newly created or updated resource.
- For writing operations POST and DELETE, a content location can be used to indicate that the body contains a status report resource in response to the requested action, which is available at provided location.
Note: When using the Content-Location header, the Content-Type header has to be set as well. For example:
  GET /products/123/images HTTP/1.1

  HTTP/1.1 200 OK
  Content-Type: image/png
  Content-Location: /products/123/images?format=raw

#### Should: Use Location Header instead of Content-Location Header
As the correct usage of Content-Location with respect to semantics and caching is difficult, we discourage the use of Content-Location. In most cases it is sufficient to direct clients to the resource location by using the Location header instead without hitting the Content-Location specific ambiguities and complexities.
More details in <a href="https://tools.ietf.org/html/rfc7231">RFC 7231</a> <a href="https://tools.ietf.org/html/rfc7231#section-7.1.2">7.1.2 Location</a>, <a href="https://tools.ietf.org/html/rfc7231#section-3.1.4.2">3.1.4.2 Content-Location</a>

#### May: Use the Prefer header to indicate processing preferences
The Prefer header defined in <a href="https://tools.ietf.org/html/rfc7240">RFC7240</a> allows clients to request processing behaviors from servers. <a href="https://tools.ietf.org/html/rfc7240">RFC7240</a> pre-defines a number of preferences and is extensible, to allow others to be defined. Support for the Prefer header is entirely optional and at the discretion of API designers, but as an existing Internet Standard, is recommended over defining proprietary "X-" headers for processing directives.
The Prefer header can defined like this in an API definition:
  Prefer:
    name: Prefer
    description: |
      The RFC7240 Prefer header indicates that particular server
      behaviors are preferred by the client but are not required
      for successful completion of the request.
      # (indicate the preferences supported by the API)

    in: header
    type: string
    required: false
Supporting APIs may return the Preference-Applied header also defined in RFC7240 to indicate whether the preference was applied.

#### May: Consider using ETag together with If-(None-)Match header
When creating or updating resources it may be necessary to expose conflicts and to prevent the lost update problem. This can be best accomplished by using the ETag header together with the If-Match and If-None-Match. The contents of an ETag: <entity-tag> header is either (a) a hash of the response body, (b) a hash of the last modified field of the entity, or (c) a version number or identifier of the entity version.
To expose conflicts between concurrent update operations via PUT, POST, or PATCH, the If-Match: <entity-tag> header can be used to force the server to check whether the version of the updated entity is conforming to the requested <entity-tag>. If no matching entity is found, the operation is supposed a to respond with status code 412 - precondition failed.
Beside other use cases, the If-None-Match: header with parameter * can be used in a similar way to expose conflicts in resource creation. If any matching entity is found, the operation is supposed a to respond with status code 412 - precondition failed.

The ETag, If-Match, and If-None-Match headers can be defined as follows in the API definition:

>    Etag:  
>        name: Etag  
>        description: The RFC7232 ETag header field in a response provides the current entitytag for the selected resource. An entity-tag is an opaque identifier for different versions of a resource over time, regardless whether multiple versions are valid at the same time. An entity-tag consists of an opaque quoted string, possibly prefixed by a weakness indicator.  <br>
>        in: header  
>        type: string   
>        required: false  
>        example: W/"xy", "5", "7da7a728-f910-11e6-942a-68f728c1ba70"  

>IfMatch: 
>  name: If-Match 
>  description: | 
>    The RFC7232 If-Match header field in a request requires the server to only operate on the resource that matches at least one of the provided entity-tags. This allows clients express a precondition that prevent the method from being applied if there have been any changes to the resource. 
>  in: header 
>  type: string 
>  required: false 
>  example:  "5", "7da7a728-f910-11e6-942a-68f728c1ba70" 
>
>IfNoneMatch: 
>  name: If-None-Match 
>  description:  
>    The RFC7232 If-None-Match header field in a request requires the server to only operate on the resource if it does not match any of the provided entity-tags. If the provided entity-tag is `*`, it is required that the resource does not exist at all. 
>  in: header 
>  type: string 
>  required: false 
>  example: "7da7a728-f910-11e6-942a-68f728c1ba70", * 

Please also see the section “Optimistic Locking in RESTful APIs” for a discussion about alternative approaches.

### API Operations
#### Must: Publish OpenAPI Specification
<todo>
All service applications must publish OpenAPI specifications of their external APIs. 

#### Should: Monitor API Usage
Owners of APIs used in production should monitor API service to get information about its using clients. This information, for instance, is useful to identify potential review partner for API changes.



    This is a regular paragraph.

    <table>
        <tr>
            <td>Foo</td>
        </tr>
    </table>

    This is another regular paragraph.

