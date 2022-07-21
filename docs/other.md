# Other

- No magic numbers.  All values other than obvious 0s, 1s, and 2s must be placed in a variable or `const`.
- Input should always be gathered in the form of virtual buttons/axes, never raw key values (except when debugging).
- Unused Unity methods should be removed.  Even empty `Start()` and `Updates()` methods cause unnecessary overhead.
- Familiarize yourself with the `Utilities.cs` and `Extensions.cs` files.  They contain many useful utilities for common situations, such as clamping, randomness, shuffling, and more.
