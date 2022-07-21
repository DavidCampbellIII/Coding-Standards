# Fields/Variables

## Accessor Types

All fields should remain as `private` as possible.  If inheritance is involved, fields should gravitate towards having `protected` access as opposed to `public` access.

`default` accessor type (ie no specified type) should never be used. When no type is specified, the accessor type assumes `internal`  (which is slightly different from `public`) for classes, and `private` for class members,   Might as well specify `private` to be more readable and explicit.

If a field must be exposed in the Inspector for easy editing access, it should be serialized.

```csharp
[SerializeField]
private int myIntField = 10;
```

This will keep the variable `private` to the rest of the codebase, but expose it to the Inspector.

If a field should be read-only, it should be made into a property.<br/>
If a field should be read-only, with the exception of the Inspector, it should be made into a property.<br/>
To see how to achieve either of these, see [properties](properties.md)
