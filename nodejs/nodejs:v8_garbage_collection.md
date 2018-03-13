# Notes On: V8 Garbage Collection

V8s garbage collection is generational and splits the JavaScript heap into a young generation and a larger old generation. The area for the young generation further uses two contiguous areas of memory (a semi-space) to store objects. Any objects created will eventually be considered either young generation or old generation, being promoted from the former to the latter if they [survive long enough](http://www.memorymanagement.org/glossary/g.html#term-generational-hypothesis).

## Young Generation

When a new object is created, it is stored in the currently active semi-space within the young generation. When the active semi-space becomes full, V8 performs a [scavenge](http://www.memorymanagement.org/glossary/c.html#term-copying-garbage-collection) operation on it, where it will clean up any objects that aren't referenced and move any still referenced objects to the other semi-space.

If an object has already been moved between semi-spaces once, V8 will then consider that object to be old generation and long living.

## Old Generation

Once the number of objects in the old generation grows beyond some (heuristically) determined limit, a [mark-and-sweep](http://www.memorymanagement.org/glossary/m.html#term-mark-sweep) operation is performed, where referenced objects are traced and marked before a sweep of the memory performed concurrently by dedicated sweeper threads cleans up everything *but* the marked objects. A mark-and-sweep doesn't move any objects like a scavenge operation does, instead it recycles the memory the object occupied, freeing it up to be used again.

Given that the [marking](http://www.memorymanagement.org/glossary/m.html#marking) portion of a mark-and-sweep needs to trace objects, it can take some time if you're attempting to mark every object in memory in one go. To minimise this, V8 [incrementally marks live objects](https://blog.chromium.org/2011/11/game-changer-for-interactive.html) in small (< 5ms) steps.

## When Does This Happen

V8 allocates garbage collection idle tasks to the scheduler, working to some deadline for completion. It then determines what operations to carry out in this period that will both free up memory, but not overrun the deadline. To meet these deadlines, average time taken for previous garbage collection operations is calculated and used to decide what operations are able to run and complete in time.

- A scavenge operation is performed on the young generation when the amount of memory being allocated is measured to become full *before* the next idle period.
- When the old generation is reaching the heap limit, incremental marking is started.
- Full garbage collection is performed if the old generation is nearly full *and* if the deadline allows for completion of the collection.
