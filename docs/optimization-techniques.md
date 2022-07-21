# Optimization Techniques

## Caching Components

Caching components is one of the best things you can do in Unity to improve/maintain the performance of a game.  Caching a component is as simple as saving off a reference of that component during the `Start()` or `Awake()` method.

*Note that the `.transform` property of gameObjects should be cached as well, as it is a near equivalent of `GetComponent<>()`, which is slow.*

```csharp
private Collider myCollider;

private void Start()
{
    myCollider = GetComponent<Collider>();
}
```

---

## Caching Objects

Just like components, objects can also be cached during `Start()` or `Awake()` to save on the overhead of instance creation.  A good example of this is `WaitForSeconds` used repeatedly in a coroutine.

```csharp
[SerializeField]
private float waitTime = 5f;

private WaitForSeconds waitForWaitTime;

private void Start()
{
    waitForWaitTime = new WaitForSeconds(waitTime);
}
```

---

## Avoid “Find” methods

Unless there is a very good reason to do so, avoid using any form of “Find” method at all costs.  These are incredibly slow, and should only ever be used in a `Start()` or `Awake()` method (or some other method that only gets called once).

Examples of “Find” methods include `GameObject.Find()`, `GameObject.FindObjectsWithTag()`, `GameObject.FindObjectWithTag()` etc.

---

## Avoid `.transform`

Instead of using `.transform` to get an object’s transform constantly, use it once to cache.  Typically, a variable called `thisTransform` can be used to achieve this.

```csharp
private Transform thisTransform;

private void Start()
{
    //or just ‘transform’ instead of ‘this.transform’, works the same way
    thisTransform = this.transform;
}
```

---

## `CompareTag()` instead of `.tag`

When a tag check is needed, use `CompareTag()` instead of checking tag equality with an object’s `.tag` property.  It is far faster, especially when used frequently.  As we will see below in the [Layer Masking](#layer-masking) section of this document, it is even faster to avoid tag checks altogether where possible.

```csharp
private void OnTriggerEnter(Collider other)
{
    if(other.CompareTag("Player"))
    {
        //do something
    }
}
```

---

## Material Instancing

All materials should be instanced whenever possible in order to speed up the rendering process.  This can be done by checking the “Enable GPU Instancing” box near the bottom of all materials using the Standard shader (as well as a lot of other custom shaders).

---

## Material Editing

When editing a material, it is best to edit a material overall instead of that specific instance of the material.

Let’s say we want to make a color change on a material.  One might be tempted to access the material like so:

```csharp
Renderer rend;
Material matToEdit = rend.material;
```

Avoid doing this whenever possible.  This will create a duplicate instance copy of the material.  Poor material management is one of the top bottlenecks for performance in game (as it is a great factor in the number of batches that are made when the game renders each frame).

If a material needs to be adjusted, consider using the `sharedMaterial` property instead.

```csharp
Renderer rend;
Material matToEdit = rend.sharedMaterial;
```

This will grab the material from the renderer without creating a duplicate instance of it.  This, however, has the downside of affecting all other objects that use this material.

If we want to change a property of a material on one specific object that uses a material that many other objects share, without modifying the look of the other objects sharing the material, it is recommended to simply create a new material and assign it to the object that must change.

This is especially true if the object’s material will be changing frequently, like a glow pulsing, light flickering etc.  If an object with a frequently changing material has its own material, then `sharedMaterial` can be used to avoid the instantiation of a new material whenever it changes.

---

## Coroutines

Coroutines are not only a way to script a series of events over a period of time, but can also be a powerful alternative to methods that execute far more frequently than is needed, and with far more overhead, such as `Update()`.

### Continuous Updates

When a function needs to update on a frame-by-frame basis, but only for a set period of time (as opposed to constantly), one can use `WaitForEndOfFrame` within a coroutine, and only start the coroutine when needed.

```csharp
private IEnumerator WaitForSomething()
{
    while(enabled)
    {
        yield return new WaitForEndOfFrame(); //better to cache this object in Start()
        //do something
    }
}
```

**IMPORTANT**: *`WaitForEndOfFrame` should only be used for graphical specific functions that require operations to be performed at the very end of a frame.<br/>If your function does not require this (most don't), `yield return null` is the appropriate alternative.*

### Delayed Updates

When a function needs to update frequently, but not necessarily exactly every frame, waiting for a short time period within a loop inside a coroutine is a more efficient alternative to the `Update()` method.

```csharp
private IEnumerator WaitForSomethingElse()
{
    while(enabled)
    {
        //0.05 equates to 20 times a second, a fairly efficient alternative to most things that
        //  need to be constantly updated
        yield return new WaitForSeconds(0.05f);
        //do something
    }
}
```

`WaitForFixedUpdate` can also be used for physics based operations, an efficient alternative to `FixedUpdate()`.

Please note that each of the above coroutine examples incorporate the use of a loop instead of recursion.  This is because C# does not support proper tail culling in recursive statements.  This means that a long running recursive function will continue to allocate stack frames, which could have the potential for issues if it runs long enough.  

Using a loop that is based on if the object is enabled or not is a good fix that achieves effectively the same thing as the infinitely recursive approach.

### When to Use Coroutines over `Update()` or `FixedUpdate()`

If deciding between using a coroutine that waits for the end of the frame (or waits `null`) and the standard `Update()` method, the best option depends on the amount of control you desire over your updating.

For example, if you require the ability to stop one part of a script from updating, but not other parts, then coroutines are your best bet, as there can be multiple coroutines associated with a single `MonoBehavior` that can be started and stopped independently from one another.

However, if your script simply requires a single constant update loop, then `Update()` is preferred due to less overhead per method call.  In fact, `Update()` should always be preferred over an infinite, slightly delayed coroutine due to the overhead associated with coroutine execution.  

Waiting about 20 times a second using a coroutine as opposed to 60 times a second using `Update()` may not increase performance if the overhead of the coroutine calls exceed whatever performance is gained by running the code in the update fewer times.  In summary, only use coroutines for performance purposes if the code being executed each time is very expensive.

---

## Layer Masking

When making use of trigger-related methods such as `OnTriggerEnter()` or `OnTriggerStay()` etc., consider if an opportunity to utilize layer masking becomes available.

Layer masking allows for physics-based operations to ignore certain predefined layers, which can help to speed up any mechanic that is reliant on these physics-based operations, especially if they are utilized frequently or in great quantities.  By only including some layers into physics calculations (such as triggers or raycasts), tag checks (or checking if needed components are on the colliding object) are avoided, speeding things up considerably.

Let’s say we have 2 layers: `Player` and `PlayerOnly`.  `PlayerOnly`, as the name suggests, only collides with objects on the `Player` layer.  When the script below is attached to an object with a trigger and the layer of `PlayerOnly`, no tag check is needed, because we know with certainty that it will only ever collide with the `Player`.

```csharp
private void OnTriggerEnter(Collider other)
{
    //because this script was placed on an object with layer PlayerOnly, 
    //no tag check is needed, and we don't need to worry about PlayerHealth not existing 
    //on the colliding object
    other.GetComponent<PlayerHealth>().DoSomething();
}
```

The downside to using layer masking instead of tag/component checking is that it is typically harder to debug, especially when many different interacting layers are involved.  It is essential to carefully track each layer and plan how it may/may not interact with another to maximize the usefulness of this technique.
