# Properties

## Accessor Types

All properties should remain as `private` as possible.  However, there are a few different common scenarios where properties might need to be `public`.  Please follow the standards listed below according to each specific situation.  
Note that not all possible situations are shown below, however one should be able to extrapolate how they should handle a unique scenario based on the examples given below.

---

Property that is `public` to read and `public` to write.  This should be used instead of a `public` variable marked with the `[HideInInspector]` attribute.

```csharp
public int myProperty { get; set; } = 10;
```

---

Property that is `public` to read, but `private` to write.

```csharp
public int myProperty { get; private set; }
```

---

Property that is `public` to read, but `private` to write with the exception of the Inspector.

Note the use of an underscore in the variable.  The property name should also match the name of the variable, excluding the underscore.

Lambda expressions should be used for properties only one line of code long.

```csharp
[SerializeField]
private int _myNum = 10;
public int myNum { get => _myNum; private set => _myNum = value; }
```

---

Property that is inherited from a `base` class, `public` to read and write.

```csharp
protected int myProperty { get; set; }
```

## `get`/`set`

The `get` and `set` of properties should be used when appropriate in order to check specific information, such as range limits for numbers, or other restraining conditions for a specific variable.  They can also be used to trigger specific events when a property is accessed or changed.

```csharp
[SerializeField]
private int _myNum = 10;
public int myNum 
{
    get 
    {
        if(someCondition)
        {
            Debug.LogError("Cannot get this variable for some reason!");
            return -1;
        }
        return _myNum;
    }

    set
    {
        if(value < 0)
        {
            Debug.LogError("myNum cannot be set to a negative number!");
        }
        else if(value > 10)
        {
            Debug.Log("myNum is greater than 10, which sets off some event of some sort!");
            EventIfMyNumOverTen();
            _myNum = value;
        }
        else
        {
            _myNum = value;
        }
    }
}
```

## Naming

Properties should be named in camelCase.
