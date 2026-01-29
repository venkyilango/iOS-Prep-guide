# iOS-Prep-guide

📱 Objective-C Interview Cheat Sheet
1️⃣ Atomic vs Nonatomic

**Atomic vs non atomic**
  By default all the properties which are defined in Objective-C are atomic. Defining a value as atomic which will guarantee you that it returns a value . Not all values returned are     valid and right .

  For ex: point = CGpoint(x: 3, y: 3)
    Thread A tries to access the point 
    THread B sets the value point = CGpoint(x: 4, y:4)
    THread C sets the value point = CGpoint(x: 5, y:5)

  All three threads tries to access the variable point at the same time . Here we cannot determine the output of the point when it is accessed by Thread A . It could be (4, 4) or (5, 5)

**Atomic (default)**

  Guarantees a value is always returned
  Does NOT guarantee the value is correct
  Uses internal locking → slower
  Not fully thread-safe

  @property (atomic, strong) NSString *name;

**➡️ Atomic = safe access, unsafe data**

**Nonatomic**

  Faster
  No locking
  Not thread-safe
  Preferred in iOS (UI runs on main thread)
  @property (nonatomic, strong) NSString *name;

2️⃣ strong vs weak vs assign
These define how ownership of an object is handled under ARC.
🧠 Ownership Rule
If you use strong, you OWN the object.
If you use weak, you DON’T own it.
If you use assign, there is no memory management.
🔷 strong
@property (strong, nonatomic) NSString *name;
Meaning
Increases retain count
Keeps object alive
Default for object types
Use when
This object should own the value
Model objects, data containers, etc.
Example
self.name = [[NSString alloc] initWithString:@"John"];
Even if no one else references "John", it stays in memory because self.name strongly owns it.
🔷 weak
@property (weak, nonatomic) id delegate;
Meaning
Does NOT increase retain count
Automatically becomes nil when object deallocates
Prevents retain cycles
Use when
Delegates
Parent references child, child references parent
UI elements referencing controllers
Example — Retain Cycle Prevention
@interface Child : NSObject
@property (weak) Parent *parent;
@end
If parent were strong, both objects would keep each other alive forever 💀
🔷 assign
@property (assign, nonatomic) int age;
Meaning
Just copies the value
No ownership
Used for primitive types
Use for
int, float, double, BOOL, struct
⚠️ Dangerous with Objects
@property (assign) NSString *name; ❌
If the string deallocates, pointer becomes dangling → crash.
🚀 Interview Summary
Type	Owns Object?	Becomes nil Automatically?	Used For
strong	✅ Yes	❌ No	Models, properties you keep
weak	❌ No	✅ Yes	Delegates, avoiding retain cycles
assign	❌ No	❌ No	Primitives
3️⃣ copy vs strong
This is about protecting object mutability.
🔷 strong
Keeps reference to the same object.
@property (strong) NSMutableString *name;
Problem
NSMutableString *str = [NSMutableString stringWithString:@"John"];
self.name = str;

[str appendString:@" Wick"];

NSLog(@"%@", self.name);  // John Wick 😱
External changes affect your property.
🔷 copy
Creates a new immutable copy of the object when set.
@property (copy) NSString *name;
Safe Version
NSMutableString *str = [NSMutableString stringWithString:@"John"];
self.name = str;

[str appendString:@" Wick"];

NSLog(@"%@", self.name);  // John ✅
Your property is protected.
📌 When to Use copy
Type	Should Use
NSString	✅ copy
NSArray	✅ copy
NSDictionary	✅ copy
Mutable versions	Usually strong
❗ Classic Interview Question
Why is NSString property usually copy?
Because someone might pass an NSMutableString.
Using copy prevents external mutation.

