# Entity-Relationship-Model

A model that functions as a high level requirements document with a structure that can be understood and communicated by different people.

## Requirment

Requirements are the needs and expectations of stakeholders that define what a system must accomplish.

In the context of an entity-relationship (ER) model, requirements are translated into entities (things of interest), attributes (properties of those things), and relationships (associations between entities).

> [!NOTE]  
> **Hands on example by _Zero-to-mastery_**
>
> 1. There is a vehicle inventory for students to rent.
> 2. There are employees at every branch.
> 3. There is maintenance for the vehicles.
> 4. There is an optional exam at the end of your lessons.
> 5. You can only take the exam twice. If you fail twice you must take more lessons.

## Entity

- A person, place or thing
- Has a singular name
- Has an identifier
- Should container more than one instance of data

> [!NOTE]  
> **Hands on example by _Zero-to-mastery_**
>
> ![entities](/06_database/database_design/entity-relationship-model-assets/driveme_entities.drawio.svg "Entities")

## Attribute

The information entities will store.

- Must be a property of the entity
- Must be atomic (can only hold a single value, e.g. address is not atomic)
- Single / multivalued (phone nr)
- Keys

## Terminologies

### Relation schema

Defines the structure of a table (e.g. a USER table has id, first_name, last_name as columns).

### Relation instance

Is the set of data that relates to the relation schema. Referring to all of the tuples / rows / data inside of the table.
