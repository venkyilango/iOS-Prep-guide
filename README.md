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
	strong → You own the object
	weak → You don’t own it
	assign → No memory management

🔷 strong
	@property (strong, nonatomic) NSString *name;
Meaning
	Increases retain count
	Keeps the object alive
	Default for object types
Use When
	Your object should own the value
	Models, data properties, containers
Example
self.name = [[NSString alloc] initWithString:@"John"];
Even if no other reference exists, the object stays alive because this property owns it.


🔷 weak
@property (weak, nonatomic) id delegate;
Meaning
    Does not increase retain count
    Automatically becomes nil when object deallocates
    Prevents retain cycles
Use When
    Delegates
    Parent–child references
    Avoiding memory leaks
Example — Retain Cycle Prevention
    @interface Child : NSObject
        @property (weak) Parent *parent;
    @end
If parent were strong, both objects would hold each other forever → memory leak.


🔷 assign
@property (assign, nonatomic) int age;
Meaning
    Direct value assignment
    No ownership
    No ARC memory handling
Use For
    int, float, double, BOOL
C structs
⚠️ Dangerous With Objects
    @property (assign) NSString *name; // ❌ Wrong
    If the string deallocates, the pointer becomes dangling → crash.




3️⃣ copy vs strong
This is about protecting against mutation.

🔷 strong
    Keeps a reference to the same object.

@property (strong) NSMutableString *name;
Problem Example

    NSMutableString *str = [NSMutableString stringWithString:@"John"];
    self.name = str;
    [str appendString:@" Wick"];
    NSLog(@"%@", self.name);  // John Wick 😱
Your property changed because the original object changed.

🔷 copy
Creates a new immutable copy when assigned.

    @property (copy) NSString *name;
Safe Example

    NSMutableString *str = [NSMutableString stringWithString:@"John"];
    self.name = str;
    [str appendString:@" Wick"];
    NSLog(@"%@", self.name);  // John ✅

External mutation does not affect your property.

📌 When to Use copy
Type	Use copy?
NSString	✅ Yes
NSArray	✅ Yes
NSDictionary	✅ Yes
Mutable versions	❌ Usually strong
Objective-C Memory Management — Advanced Topics
    4️⃣ Retain Cycles
    🧠 What is a Retain Cycle?
        A retain cycle happens when two objects strongly reference each other, so neither can be deallocated.
        ARC cannot break retain cycles automatically.

❌ Problem Example

    @interface Person : NSObject
        @property (strong) Car *car;
    @end

    @interface Car : NSObject
        @property (strong) Person *owner;
    @end

    Person *p = [[Person alloc] init];
    Car *c = [[Car alloc] init];

    p.car = c;     // Person strongly owns Car
    d.owner = p;   // Car strongly owns Person

✅ Solution: Use weak

    @interface Dog : NSObject
        @property (weak) Person *owner;
    @end


5️⃣ ARC vs MRC
Feature	ARC	MRC
Memory management	Automatic	Manual
Use retain/release	❌ No	✅ Yes

🔷 Manual reference counting

You manage memory yourself.

Person *p = [[Person alloc] init];  // retain count = 1

[p retain];   // retain count = 2
[p release];  // retain count = 1
[p release];  // retain count = 0 → dealloc called

If you forget release → memory leak If you over-release → crash




🔷 Automatic reference counting

Compiler inserts memory calls for you.

Person *p = [[Person alloc] init];
When no strong references remain → ARC deallocates automatically.
You still control ownership using strong, weak, retian and copy




6️⃣ Autorelease Pool
    What is an Autorelease Pool?
        A memory pool that holds temporary objects and releases them later.
Used heavily in:
    Event loops
    Background threads
    Loops creating many objects

🔷 How It Works (Concept)

NSString *str = [[[NSString alloc] initWithString:@"Hello"] autorelease];
Object is added to autorelease pool → released when pool drains.

🔷 ARC Version
ARC still uses autorelease pools behind the scenes.

@autoreleasepool {
    NSString *str = [NSString stringWithFormat:@"Hello"];
}
When the block ends → temporary objects released.

❗ Why It Matters (Interview Favorite)
Without autorelease pool in loops → memory spikes
❌ Bad Example

for (int i = 0; i < 100000; i++) {
    NSString *str = [NSString stringWithFormat:@"Number %d", i];
}
Memory grows until loop ends.

✅ Correct Way

for (int i = 0; i < 100000; i++) {
    @autoreleasepool {
        NSString *str = [NSString stringWithFormat:@"Number %d", i];
    }
}
Temporary objects are released every iteration.



