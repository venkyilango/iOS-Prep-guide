# iOS-Prep-guide

📱 Objective-C Interview Cheat Sheet
1️⃣ Atomic vs Nonatomic

**Atomic vs non atomic**
By default all the properties which are defined in Objective-C are atomic. Defining a value as atomic which will guarantee you that it returns a value . Not all values returned are valid and right .

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


