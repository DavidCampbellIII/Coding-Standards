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

## Data Types

All fields (and any other variable for that matter) should be explicit when declaring a data type.  No local variable should use `var` for its data type.

`double`s are not used in much of Unity’s API, so `float`s should be used in all cases instead.

## Naming

Fields/variables should be named in camelCase.

## Tooltips

In order to create the easiest to use API from the code to the front end development and designing of the game, add tooltip attributes to make viewing the purpose of the field from the Inspector as easy and seamless as possible.

All exposed fields should have a tooltip, unless they are extremely self-explanatory.  When in doubt, it never hurts to be specific about the purpose of the field!

For the best readability, tooltip attributes should always be placed last in the attribute list, and on their own separate line.  They should be indented as to provide a visual break between the other attributes.

```csharp
[SerializeField,
     Tooltip("Current health of the player")]
private float currentHealth = 100f;
```

## Warning Suppression

Warnings can certainly be useful, but sometimes they are annoying, as they don’t always interface well with Unity’s workflow.  Specific, warning 0649 about how a variable is declared but never initialized can be frustrating, as a lot of variables are declared in code, and then assigned a value through Unity’s Inspector.  Suppressing these warnings is easy to do, and keeps the console clean.  

Simply add `#pragma warning disable <WARNING_CODE>` to the start of where the warning should be suppressed.

Be sure to restore the error after all variables intended to suppress to avoid suppressing the warning throughout the entire file.  This can be done by simply writing `#pragma warning restore <WARNING_CODE>`.

```csharp
#pragma warning disable 0649
[SerializeField]
private int num; //to be assigned in Inspector
[SerializeField]
private GameObject someObject; //to be assigned in Inspector
#pragma warning restore 0649 
//be sure to restore the error after all intending variables so it is not accidentally 
//  suppressed throughout the entirety of the file
```
