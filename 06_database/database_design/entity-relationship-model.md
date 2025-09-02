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
- Must be atomic, must be broken down to its most useful level of detail (can only hold a single value, e.g. address is not atomic)
- Single / multivalued (phone nr)
- Keys

> [!NOTE]  
> **Hands on example by _Zero-to-mastery_**
>
> **School:**
> school_id, street_name, street_nr, postal_code, state_city  
> **instructor**:
> teacher_id, first_name, last_name, date_of_birth, hiring_date, school_id  
> **student**:
> student_id, first_name, last_name, date_of_birth, school_id  
> **exam**:
> student_id, teacher_id, date_taken, passed, lesson_id  
> **lesson**:
> lesson_id, date_of_enrollment, package, student_id

## Terminologies

**Relation schema**

Defines the structure of a table (e.g. a USER table has id, first_name, last_name as columns).

**Relation instance**

Is the set of data that relates to the relation schema. Referring to all of the tuples / rows / data inside of the table.

**Relation key**
Uniquely identify each row in a table. Which is needed when you want to create relationships between tables.

**Super key**
Any combination of attributes that uniquely identify a row.

**Candidate key**
The minimal amount of attributes needed to uniquely identify a row.

**Primary key**
Practical implementation of a candidate key and a relation key that uniquely identifies the relationship.

**Foreign key**
References the primary key of another table in order to create a relationship between two tables. The foreign key can only take values of the primary key of the related table.

**Compound key**
Uniquely identify rows by using foreign key.

**Composite key**
Uniquely identify rows without using foreign key.

**Surrogate key**
Defining a separate column that uniquely identifies the row without using an existing row(s).

## Relationship

Tables are linked by primary key and foreign key.

From conceptional level:
A relationship links two entities togethere.
It as a parent entity and a child entity (one entity is driving the relationshipt towards the other entity).
Has a cardinality (1:1, 1:many, many:many)
Has modality, does it have to have a value in the relationship or not? (null, not null)
Is dependent or independent on other entity

### Crowsfeet

<span style="color: red;">Relationship</span>
<span style="color: blue;">Constraint</span>

![alt text](image-1.png) <span style="color: red;">zero (= optional)</span> or <span style="color: blue;">more</span>

![alt text](image-2.png) <span style="color: red;">one (=must)</span> or <span style="color: blue;">more</span>

![alt text](image-3.png) <span style="color: red;">one (= must)</span> and <span style="color: blue;">only one</span>

![alt text](image-4.png) <span style="color: red;">zero (= optional)</span> or <span style="color: blue;">one</span>

> [!NOTE]  
> **Hands on example by _Zero-to-mastery_**
>
> ![alt text](/06_database/database_design/entity-relationship-model-assets/entity-relationships.drawio.svg)

### Many to many

As a rule of thumb always try to resolve a many:many.

Many:many relationships can be solved by intermediate tables.

A good example is a relationship between author and book. One book can have multiple authors and one author can have multiple books.

If you do not resolve this many:many relationship you would end up adding a column for every author to books table and vice verse you would add a new column for every book a author writes.

Solution: Intermediate book-author entity which defines the relationship between one book and one author.

> [!IMPORTANT]  
> For the **Hands on example** the _lesson_ is an intermediate entity that resolved a many:many relationship between student and instructor.

## Subject areas

Divide entities into logical groups that are related. This gives you the opportunity to separate the requirements in individual buckets. Plus you can split up the groups in different teams.

Rules:

- All entities must belong to one area
- An entity can only belong to one area
- Nested areas are allowed

> [!NOTE]
>
> **Hands on example by _Zero-to-mastery_** > ![alt text](/06_database/database_design/entity-relationship-model-assets/entity-subject-areas.drawio.svg)
