# Methods

## Accessor Types

All methods should remain as `private` as possible.  `default` accessor type (ie no specified type) should never be used, even with default Unity methods. 

When no type is specified, the accessor type assumes `internal`, which is slightly different from `private`.  Might as well specify `private` to be more readable anyhow.

```csharp
private void Start()
{
    //empty
}

private void MyCustomMethod(Collider col)
{
    //empty
}
```

## Naming

Methods should be named in PascalCase.
