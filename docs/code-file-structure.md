# Code File Structure

Each script file should be laid out in the same general manner.

```csharp
//namespaces
using UnityEngine;
using System;

//aliases
using Random = UnityEngine.Random;

public class MyClass
{
    //consts, most accessible to least accessible
    public const int INT_CONST = 10;
    private const string FAVE_WORD = "Pie";

    //statics, public only
    public static float someStaticNum = 69.420f;

    //region named "Nested Structures" containing all nested classes, structs, enums etc.
    #region Nested Structures

    private class SomeNestedClass
    {
        public SomeNestedClass()
        {
            Debug.Log("Created nested class!");
        }
    }

    private enum SomeNestedEnum
    {
        ENUM_ELEMENT_1, ENUM_ELEMENT_2
    }

    #endregion

    //exposed fields and their properties, if any
    [SerializeField,
        Tooltip("This is an int, it is used for nothing")]
    private int _someInt;
    public int someInt { get => _someInt; private set => _someInt = value; }

    [SerializeField, Range(0f, 10f),
        Tooltip("A ranged float with no associated property")]
    private float floatWithoutProperty;

    [SerializeField]
    private string _firstName = "DefaultFirstName";
    public string firstName => _firstName;

    [SerializeField]
    private string _lastName = "DefaultLastName";
    public string lastName => _lastName;

    //properties that perform meaningful calculations or operations
    //  beyond simple assignments
    public string FullName => $"{firstName} {lastName}";

    //auto properties
    public Transform thisTransform { get; private set; }

    //private fields
    private int privateInt = 1;
    private bool privateBool;

    //private static fields
    private static bool privateStaticBool = false;

    //methods relating to init, Start, Awake, OnEnable etc.
    private void Start()
    {
        //caching
        thisTransform = this.transform;
    }

    //methods relating to Update, FixedUpdate, LateUpdate etc.
    private void Update()
    {
        privateInt++;
    }

    //any other methods
    public void SomeMethod()
    {
    }

    private void AnotherMethod()
    {
    }

    //debugging methods
    #region Debugging

//preprocessor statements should always be left aligned
//so that stand out from #regions
#if UNITY_EDITOR
    private void OnDrawGizmos()
    {
    }

    private void SomeOtherDebugMethod()
    {
    }
#endif

    #endregion
}
```
