# Do we need a type of "Labels in this record?"

Typescript has `[keyof R]`, which is an untagged union `"key1" | "key2" | ...` of all the keys in record R. `R[keyof R]` is the untagged union of values `V1 | V2 | ...`, and also represents the result of accessing an unknown valid key in the record.

It seems like there are cases where we would like to be able to store one or more labels, about which we know only that, for some known `r`, each l satisfies `{l := Unit} < Labels r`.

## Main Idea

- `l : KeyOf r` could be an untagged union of labels (or at least should behave like one).
- The equivalent of TypeScript's `R[keyof R]` could retain the label, so that the term `sel r l : Sigma r`.
- so we wouldn't need to have untagged unions of values.

- However, is this necessary?
  - It seems like it would be helpful to have a list of labels that appear in a row, but I don't yet have a clear example where we definitely need this.
  - Seems related to converting between strings and labels, but not sure if there is a dependency.
  - I've flagged some of the functions in the TableAPI spec as possibly needing one or the other.
  - The main question is whether we can accomplish all of this using the existing tools, like `Labels`, `ana`, `syn`, etc.