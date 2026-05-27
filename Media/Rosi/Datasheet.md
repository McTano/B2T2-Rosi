## Reference

> Q. Where can we learn about the programming medium covered by this datasheet?
> (Feel free to link to multiple kinds of artifacts: repositories, papers, videos, etc.
> Please also include version information where applicable.)

[Rosi Language Repo](github.com/IowaFP/Rosi).

Rosi is primarily based on these two research papers:
- [Abstracting extensible data types: or, rows by any other name](https://dl.acm.org/doi/10.1145/3290325) - Morris & McKenna, 2019.
- [Generic Programming with Extensible Data Types: Or, Making Ad Hoc Extensible Data Types Less Ad Hoc](https://dl.acm.org/doi/10.1145/3607843) - Hubers & Morris, 2023.

This paper is also relevant:
[Extensible Data Types with Ad-Hoc Polymorphism](https://dl.acm.org/doi/10.1145/3776662) - Toohey et al., 2026.


> Q. What is the URL of the version of the benchmark being used?


> Q. On what date was this version of the datasheet last updated?
May 26, 2026

> Q. If you are not using the latest benchmark available on that date, please explain why not.


## Example Tables

> Q. Do tables express heterogeneous data, or must data be homogenized?

Tables can be heterogeneous.


> Q. Do tables capture missing data and, if so, how? Do missing values affect the output constraints of any operations, for example `groupBy`?

By default, all data must be present. Nullable columns must be represented explicitly by the `Maybe` type. There is no special handling for columns containing `Maybe` except in the operations grouped under `Missing Values` in the Table API, which explicitly deal with missing data.

In `groupBy`, for example, the `Eq` argument must contain an equality function that operates over `Maybe` for columns that are nullable.

> Q. Are mutable tables supported? Are there any limitations?

All data in Rosi is immutable.


> You may reference, instead of duplicating, the responses to the above questions in answering those below:

> Q. Which tables are inexpressible? Why?
TODO, but all tables should be expressible, with the caveat that nullable columns must be made explicitly nullable, so `studentsMissing` cannot have the same type as `students` unless both are made nullable in the `age` and `favorite color` fields.


> Q. Which tables are only partially expressible? Why, and what’s missing?

The order of columns in a table is determined by a lexicographic ordering of the column names (labels), and cannot be specified by the programmer.
As a result, the expected order of columns is unexpressible for all tables.

> Q. Which tables’ expressibility is unknown? Why?

TODO

> Q. Which tables can be expressed more precisely than in the benchmark? How?

The schema of each table is clearly expressed in its type.


> Q. How direct is the mapping from the tables in the benchmark to representations in your system? How complex is the encoding?

The encoding is quite simple.

A table is represented as a list of table rows. A table row is a record whose structure matches the schema of the table.

<!-- TODO explain the use of labels as column names and their limitations -->

## TableAPI

> Q. Are there consistent changes made to the way the operations are represented?

A `Seq<ColName>` is consistently represented as an unordered record where each column name (as a label) is mapped to the `Unit` type (encoded in Rosi as an empty record). The only exception to this pattern is in `sortByColumns`, where a list of column names is used instead (see `sortByColumns` implementation in `TableAPI.ro`).

Wherever `Number` is required, we generally use `Nat`, except in `head` where special behavior is defined for negative values, so we use `Int` instead.

> Q. Which operations are entirely inexpressible? Why?

Implementation of the following operations are currently blocked by type errors, which may be resolvable in the current version of Rosi, or may require changes to Rosi. See notes in `TableAPI.ro` for details.

- `pivotTable`: various errors depending how I attempt to express the type.
- `sortByColumns`: Can't convince the typechecker that each indicated column must be contain a number.

> Q. Which operations are only partially expressible? Why, and what’s missing?

We cannot express the constraints based on:

- the number of rows in a table
- the length of sequences
- whether an index is in-bounds for a table or sequence.
- duplicates in sequences of values, or in tables, which are encoded as sequences of rows (specifically, lists of records).

However, we do enforce constraints on duplicate column names in table headers or sequences of column names (actually unordered records; see first answer in this section). These constraints are usually implicit, and are entailed by either:

a. records cannot contain duplicate labels, or

b. the constraint `r1 + r2 ~ r3` (meaning "r3 is the concatenation of r1 with r2") is only satisfiable when the labels in r1 and r2 are disjoint.

In addition, the following operations currently present problems. See notes in `TableAPI.ro` for details.

- `getColumns`: first overload requires ordered columns.
- `selectColumns`: the first two overloads are not expressible because they require both:
  1. ordered columns, and
  2. a result type which is determined based on the values in the input sequence, which would require dependent types.
- `renameColumns` and `pivotTable` are supposed to be able to operate over multiple tables at once. However, the former has only been successfully defined over a single column at a time. The same should be true for the latter, except that the single-column version also has issues (see previous answer).
- `dropna`: This operation can only be applied if all columns in a table are nullable. Unfortunately, there is also currently no way to convert a partially nullable table schema into a fully nullable one. Solving either problem would probably require something similar to the `Split` constraint as described in *Toohey et al. 2026*.

> Q. Which operations’ expressibility is unknown? Why?

- `orderBy`: Requires existential types to allow the `K` type to vary within the sequence of `(getKey, compare)` pairs. This is probably solvable in the current version of Rosi by an ad-hoc existential type. We have a demo of this approach in the Rosi repo, but applying this solution is TODO.


> Q. Which operations can be expressed more precisely than in the benchmark? How?

None. (Probably)

## Example Programs

> Q. Which examples are inexpressible? Why?


> Q. Which examples’ expressibility is unknown? Why?


> Q. Which examples, or aspects thereof, can be expressed especially precisely? How?


> Q. How direct is the mapping from the pseudocode in the benchmark to representations in your system? How complex is the encoding?


## Errors

> There are (at least) two parts to errors: representing the source program that causes the error, and generating output that explains it. The term “error situation” refers to a representation of the cause of the error in the program source.
> 
> For each error situation it may be that the language:
> 
> - isn’t expressive enough to capture it
> - can at least partially express the situation
> - prevents the program from being constructed
> 
> Expressiveness, in turn, can be for multiple artifacts:
> 
> - the buggy versions of the programs
> - the correct variants of the programs
> - the type system’s representation of the constraints
> - the type system’s reporting of the violation

> Q. Which error situations are known to be inexpressible? Why?


> Q. Which error situations are only partially expressible? Why, and what’s missing?


> Q. Which error situations’ expressibility is unknown? Why?


> Q. Which error situations can be expressed more precisely than in the benchmark? How?


> Q. Which error situations are prevented from being constructed? How?


> Q. For each error situation that is at least partially expressible, what is the quality of feedback to the programmer?


> Q. For each error situation that is prevented from being constructed, what is the quality of feedback to the programmer?

