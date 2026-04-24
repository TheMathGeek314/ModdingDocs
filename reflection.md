# Reflection

If you need to read, write, or invoke private fields and methods, this is possible to do with reflection. Usually they are private for a reason, so be sure you know what you're doing.

To access a nonpublic field, you can define a `FieldInfo` object to reference a class's field by name. This is an expensive process, so it is good practice to define the field once and only access its value at runtime. To do this, call `typeof(<the class you want>).GetField(<the field name>, <the corresponding BindingFlags>);`
In most cases, the flags you'll use are `BindingFlags.NonPublic | BindingFlags.Instance` for private instance fields, but for static fields, swap `Instance` with `Static`.

A FieldInfo object holds a reference to the class itself, not a specific object of that class, so to retrieve or modify the value, you'll need to supply the actual instance of the object you intend to use. If you're using a static field, simply pass `null` instead. Fields can be read with `.GetValue(object obj)` and set with `.SetValue(object obj, object value)` as shown below. These are generalized methods that take and return `object` type, so be sure to cast as needed.
```cs
static FieldInfo camTargetVelocityX;

// During initialization
camTargetVelocityX = typeof(CameraTarget).GetField("velocityX", BindingFlags.NonPublic | BindingFlags.Instance);

// Read value at runtime
Vector3 velocity = camTargetVelocityX.GetValue(GameCameras.instance.cameraTarget) as Vector3;

// Set value to (5,0,0) at runtime
camTargetVelocityX.SetValue(GameCameras.instance.cameraTarget, new Vector3(5, 0, 0));
```

Calling a nonpublic method is very similar to referencing a nonpublic field, but instead of `FieldInfo`, you define your own `MethodInfo` object with nearly identical setup (replacing `GetField` with `GetMethod`). To call the method at runtime, you need to pass the instance of the class and an array of the parameters it needs into the `Invoke()` method. If the method is static, once again pass `null`, and if it takes no parameters, pass a second `null`.
```cs
MethodInfo jump;
MethodInfo enableCursor;

// During initialization
jump = typeof(HeroController).GetMethod("Jump", BindingFlags.NonPublic | BindingFlags.Instance);
enableCursor = typeof(InputHandler).GetMethod("SetCursorEnabled", BindingFlags.NonPublic | BindingFlags.Static);

// Call at runtime
jump.Invoke(HeroController.instance, null);
enableCursor.Invoke(null, [true]);
```

It is perhaps worth noting that reflection can also access public fields and methods, which is less common but has its uses if you want to find things by name.


### TODO
- Explain Instance, Static, public, non public
- Explain reflection (please use HC/GM to explain instead of random placeholder names)
- Use of reflection helper
- How to get/set private fields
  - How to get/set fields in classes (eg how to set HeroController.rb2d.velocity (get RigidBody2D and use that))
- How to get/set private properties
- How to call private functions
  - Showcase new MAPI ReflectionHelper for methods
