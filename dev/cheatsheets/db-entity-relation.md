# Database Entity Relationships

## ERD Symbols and Notations

Some common symbols and notations used in Entity Relationship Diagrams (ERDs):

- ENTITY: Represents an entity, which is a person, place, thing, or event in the real world that is represented in the database. It is usually represented by a rectangle with the name of the entity written inside.
- ATTRIBUTE: Represents a property or characteristic of an entity, such as its name, age, or address. It is usually represented by an oval with the name of the attribute written inside.
- PRIMARY KEY: Represents the unique identifier for an entity, which is used to distinguish one entity from another. It is usually represented by an underline below the attribute name.
- FOREIGN KEY: Represents a reference to the primary key of another entity, which is used to link the two entities together. It is usually represented by an arrow pointing from the foreign key attribute to the primary key attribute it references.
- RELATIONSHIP: Represents the association between two or more entities. It is usually represented by a diamond with the name of the relationship written inside.
- CARDINALITY: Represents the number of instances of an entity that can be associated with instances of another entity. It is usually represented by numbers written near the ends of the relationship line.
- ONE-TO-ONE: A relationship in which each instance of an entity is associated with one and only one instance of another entity.
- ONE-TO-MANY: A relationship in which each instance of an entity is associated with one or more instances of another entity.
- MANY-TO-MANY: A relationship in which each instance of an entity can be associated with one or more instances of another entity, and vice versa. This is usually implemented using a junction or association table.

## One-to-One Entity Interaction

Each record in a table is associated with **only** one record in another table.

### The 1:1 Interaction

A `user` HAS Only ONE `profile`

1. Each `user` *has* exactly one `profile`,
2. Each `profile` *is associated with* exactly one `user`.

To model this relationship in a **PostgreSQL** database, we can add a *foreign key* to the `profile` table that references the `user` table. Specifically, we can add a `user_id` column in the profiles table that is a *foreign key* and references the `user_id` column in the `user` table, like this:

    CREATE TABLE user (
    user_id     SERIAL PRIMARY KEY,
    user_name   VARCHAR(50)
    );

    CREATE TABLE profile (
    profile_id      SERIAL PRIMARY KEY,
    profile_user_id INTEGER UNIQUE REFERENCES user(user_id),
    profile_bio     TEXT
    );

In this example, the `user_id` column in the profiles table is a *foreign key* that references the `user_id` column in the `user` table. The **`UNIQUE`** constraint ensures that each `user` is associated with at most one `profile`, and vice versa.

---

Dummy data that could be stored in the `user` and `profile` tables:

user
user_id |  user_name
--------|-----------
1       |  Adrian Mróz
...     |  ...
12346 |  Axiothéa Phleiasía
12347 |  Andrzej Duda
12348 |  Donald J. Trump, Jr.
12349 |  Elizabeth II

profile
profile_id | profile_user_id | profile_bio
-----------|----------------|-------------
0          | 1              | "Certified Coldcooler"
...        | ...            | ...
68         | 12346          | "I once cross-dressed so I could study with Plato!"
69         | 12347          | "President of the Republic of Poland"
70         | 12348          | "Son of Donald J. Trump"
71         | 12349          | "Queen of the United Kingdom"

In this data, we can see that each `user` has exactly one `profile`, and each  `profile` is associated with exactly one `user`. 

The `profile_user_id` column in the  `profile` table references the `profile_id` column in the `user` table, and the **UNIQUE** constraint ensures that each `user` is associated with at most one `profile`.

## One-to-Many

Each record in one table is associated with one or many records in another table.

### How 1:n Entities Interact

1. A `band` *RELEASES* MANY `album`
2. An `album` *BELONGS* TO One `band`

Each `band` has more than one `album`, but each `album` belongs to only one `band`.

To model this relationship in a **PostgreSQL** database, we can add a *foreign key* to the `album` table that references the `band` table. Specifically, we can add a `band_id` column in the `album` table that is a *foreign key* and references the `band_id` column in the `band` table, like this:

    CREATE TABLE band (
    band_id     SERIAL PRIMARY KEY,
    band_name   VARCHAR(50),
    band_genre  VARCHAR(50)
    );

    CREATE TABLE album (
    album_id            SERIAL PRIMARY KEY,
    album_name          VARCHAR(50),
    album_release_date  DATE,
    album_band_id       INTEGER REFERENCES band(band_id)
    );

In this example, the `album_band_id` column in the `album` table is a *foreign key* that references the `band_id` column in the `band` table.

#### Data Example

Some data that could be stored in the bands and albums tables:

#### Band

id | band_name          | band_genre
---|--------------------|-----
1  | System of a Down   | Alternative
2  | Pink Floyd         | Classic Rock
3  | Eminem             | Rap
4  | Beyonce            | Pop
5  | Kendrick Lamar     | Hip-hop
6  | Babayaga Ojo       | Punk

#### Album

id | album_name                  | album_release_date | album_band_id
---|-----------------------------|--------------|--------
1  | Toxicity                    | 2001-05-15   | 1
2  | Mezmerize/Hypnotize         | 2005-11-08   | 1
3  | The Wall                    | 1979-11-30   | 2
4  | Dark Side of the Moon       | 1973-03-01   | 2
5  | The Marshall Mathers LP     | 2000-05-23   | 3
6  | Lemonade                    | 2016-04-23   | 4
7  | DAMN.                       | 2017-04-14   | 5
8  | Siła                        | 2007-05-30   | 6
9  | Various - ANTIFA BENEFIT    | 2008-11-11   | 6

In this data, we can see that each `band` has many `album`, but each `album` belongs to only one `band`. The `band_id` column in the `album` table references the `band_id` column in the `band` table, creating a one-to-many relationship between the two tables.

In our example data, some `band`s have one `album` and others have many. For example, System of a Down has two albums, while Eminem has one album.

## Many-to-Many

1. A `student` *ENROLLS* IN MANY `class`
2. A `class` *HAS* MANY `student`

### How n:n Entities Interact

Each record in one table can be associated with many records in another table, and vice versa.

A common example of a many-to-many relationship is between `student` and `class`. A `student` can be enrolled in many `class`, and each `class` can have many `student` enrolled. To model this relationship in a **PostgreSQL** database, we can create a third table, known as a *junction* or *pivot table*, to link the two tables together.

Here is an example schema for the `student`, `class`, and `enrollment` tables:

    CREATE TABLE student (
    student_id          SERIAL PRIMARY KEY,
    student_first_name  VARCHAR(50),
    student_last_name   VARCHAR(50)
    );

    CREATE TABLE class (
    class_id            SERIAL PRIMARY KEY,
    class_name          VARCHAR(50),
    class_teacher_name  VARCHAR(50)
    );

    CREATE TABLE enrollment (
    enrollment_id           SERIAL PRIMARY KEY,
    enrollment_student_id   INTEGER REFERENCES student(student_id),
    enrollment_class_id     INTEGER REFERENCES class(class_id),
    grade                   VARCHAR(2)
    );

In this example, the `enrollment` table serves as a junction table between the students and classes tables. We could have named the `enrollment` table as `student_class` or `class_student`, but the name `enrollment` is more descriptive.

It contains foreign keys to both tables (`student_id` and `class_id`), as well as additional information about each enrollment (the grade column).

Here is an example of some data that could be stored in the `student`, `class`, and `enrollment` tables:

Students:
student_id | student_first_name | student_last_name
---|------------|---------
1  | Harry      | Potter
2  | Ron        | Weasley
3  | Hermione   | Granger
4  | Draco      | Malfoy
5  | Luna       | Lovegood

Class:
class_id | class_name      | class_teacher_name
---------|----------------|-------------
1  | Potions         | Snape
2  | Defense Against the Dark Arts | Lupin
3  | Charms          | Flitwick
4  | Transfiguration | McGonagall
5  | Herbology       | Sprout

Enrollment:
enrollment_id | enrollment_student_id | enrollment_class_id | enrollment_grade
---|------------|----------|-------
1  | 1          | 1        | A-
2  | 1          | 2        | B+
3  | 1          | 3        | A
4  | 2          | 2        | A
5  | 3          | 1        | B-
6  | 3          | 2        | A-
7  | 3          | 3        | A
8  | 4          | 1        | C+
9  | 5          | 5        | B+

In this data, we can see that Harry Potter is enrolled in three classes (Potions, Defense Against the Dark Arts, and Charms), while Hermione Granger is enrolled in all five classes. The enrollments table links the two tables together, creating a many-to-many relationship between them. The grade column in the enrollments table stores additional information about each enrollment, such as the student's grade in the class.
