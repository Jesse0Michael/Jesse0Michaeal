# Domain Model Design

A domain model is the abstraction of knowledge from both the technical and business experts
(engineering and product).

Domain Driven Design starts with the knowledge of the business entities and values, and
builds around those models.

Communication around these models should be done with a shared [Ubiquitous Language](#ubiquitous-language)
that both technical and business experts use. This language should be reflected in the [models](#models,
[entities](#entities), [value objects](#value-objects) and [services](#service) and should be consistent in the code as well as internal messaging and
discussions.

The functionality of the system is further broken into separate layers, isolated with distinct
responsibilities, through a [Layered Architecture](#layered-architecture).

To organize the domain layer, models are isolated into their own [bounded context](#bounded-context) and separated distinct
[modules](#modules). Management and interaction with these models is been delegated to their
respective module APIs.

## Ubiquitous Language

> On a project without a common language, developers have to translate for domain experts. Domain experts translate between
> developers and still other domain experts. Developers even translate for each other. Translation muddles model concepts, which
> leads to destructive refactoring of code. The indirectness of communication conceals the formation of schisms - different team
> members use terms differently but don’t realize it. This leads to unreliable software that doesn’t fit together. The effort of translation
> prevents the interplay of knowledge and ideas that lead to deep model insights.
>
> -- Eric Evans, Domain-Driven Design

A Ubiquitous Language should be documented and shared between both technical and business experts.
The language should be updated as frequently as the models are updated.
If code or documentation is inconsistent with the Ubiquitous Language, it should be refactored to match.

## Models
The models that we define through collaborative knowledge transfer will change and evolve as
deeper learnings and new business requirements are discovered. The models and the
language we use should be refactored to match the changes in the domain.

> These models are never perfect; they evolve. They must be practical and useful in making sense of the domain. The must be rigorous enough to make the application simple to implement and understand.
>
> -- Eric Evans, Domain-Driven Design

### Entities
> An object defined primarily by its identity is called an entity. They have life cycles that can radically change their form and content,
> but a thread of continuity must be maintained. Their definitions, responsibilities, attributes, and associations should resolve around
> who they are, rather than the particular attributes they carry.
>
> -- Eric Evans, Domain-Driven Design

An object that is defined by its identity. May consist of
other value objects, a life cycle, state, or relationships to other
entities. These are the foundational blocks of the domain.
When modeling aim for one-directional relationships; bi-directional relationships add a
high level of complexity that should be avoided if possible.
Ex: Room, User, Media, etc...

### Value Objects
> An object that represents a descriptive aspect of the domain with no conceptual identity is called a value object. Value objects are
>instantiated to represent elements of the design that we care about only for what they are, not who or which they are.
>
> -- Eric Evans, Domain-Driven Design

An object, with no conceptual identity, that
represents a descriptive aspect of the domain.
Ex: Address, Relationship, etc...

## Service
A stateless operation behind a defined interface that
interacts with the entities and value objects of the domain
Ex: A client that looks up room activity and translates it to a feed

## Modules 
The cohesive set of concepts that the model is broken
into to provide a package that has low coupling and high cohesion.
Ex: Users are packed into the Users API module

## Bounded Context 
The fixed boundary set for a model in the scope
of the modules in a domain.
Models can only be manipulated or extended within their bounded context to keep the
model pure.
Ex: User Management is confined to the context of Users API

## Entity Relationships

``` mermaid
erDiagram
    USER ||--o{ POST : "creates"
    USER ||--o{ COMMENT : "writes"
    POST ||--|{ COMMENT : "has"
    USER ||--o{ FRIENDSHIP : "establishes"
    FRIENDSHIP ||--o{ USER : "involves"

```

## Layered Architecture

> When the domain-related code is diffused through such a large amount of other code, it
> becomes extremely difficult to see and reason about. Superficial changes to the UI can
> actually change business logic. To change a business rule may require meticulous tracing
>
> of UI code, database code, or other program elements. Implementing coherent, model-
> driven objects becomes impractical. Automated testing is awkward. With all the
>
> technologies and logic involved in each activity, a program must be kept very simple or it
> becomes impossible to understand.
>
> -- Eric Evans, Domain-Driven Design

Separating software into understandable components that reflect the models of the business
domains is just one portion of Domain Driven Design. Separating the concerns of
software into different architectural layers allows for applications to concentrate on their
designed functionality in isolation of the rest of the system. Defining the layers of the
architecture controls the flow of the different pieces in the system.

``` mermaid
flowchart TD
A[User Interface] --> B[Application]
B --> C[Domain]
C --> D[Infrastructure]
```

### User Interface
Responsibilities include showing information to the user and interpreting user commands. 
Also known as the presentation layer.

### Application
Directs the interaction and coordinates tasks with domain objects to work out business
problems, but does not contain business rules or knowledge.

### Domain
Responsible for representing concepts of business domains. The models, roles, and rules that
make up the heart of the business.

Proprietor of the models used in the business' Ubiquitous Language within the engineering systems.

### Infrastructure
Provides the technical capabilities that support the overarching layers.
