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

---

## Naming

Properties should be named in either camelCase or PascalCase, depending on their purpose.

### camelCase

If a property is a basic getter or setter with minimal logic, it should be camelCase.

This is to signify that the property is nothing more than a getter/setter for a field, with no significant logic.

#### camelCase Example 1

Basic getter property, no advanced logic.

```csharp
[SerializeField]
private int _num;
public int num => _num;
```

#### camelCase Example 2

Basic getter/setter property, no advanced logic.

```csharp
[SerializeField]
private int _num;
public int num { get => _num; private set => _num = value; }
```

#### camelCase Example 3

Basic getter/setter property, only simple checks.

```csharp
[SerializeField]
private int _num;
public int num 
{ 
    get => _num; 
    private set
    {
        if(value != 0)
        {
            _num = value;
        }
    }
}
```

### PascalCase

If a property performs some sort of meaningful calculation beyond the scope of the average getter or setter, it should be PascalCase.

This is to signify that the property does meaningful work when used.

#### PascalCase Example 1

Getter property does meaningful work and creates a new string.

```csharp
[SerializeField]
private string firstName;
[SerializeField]
private string lastName;

public string FullName => $"{firstName} {lastName}";
```

#### PascalCase Example 2

Getter property does meaningful work and can create a different result each time depending on the current `.position` of `Transform t`.

```csharp
[SerializeField]
private Transform t;
[SerializeField]
private Vector3 offset;

public Vector3 TargetPos 
{
    get 
    {
        if(t)
        {
            return t.position + offset;
        }
        return Vector3.zero;
    }
};
```

#### PascalCase Example 3

Property with simple getter, but with a setter doing meaningful work that may involve a method call and object state change.

```csharp
private int _num;

public int Num 
{
    get => _num;
    set 
    {
        if(value % 3 == 0)
        {
            SomeMethod();
        }

        _num = Mathf.Clamp(value, -100, 100);
    }
};
```
