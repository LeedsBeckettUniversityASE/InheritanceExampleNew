# Shapes: a journey through object-oriented design

This Windows Forms project demonstrates how a small shapes-drawing example can evolve through a series of focused changes. Follow the commit history to see the design move from separate, unrelated classes to shared inheritance, polymorphic handling, abstract classes and methods, and an interface.

The examples use circles, squares, rectangles, and triangles. As you read each stage, consider how a change affects duplicated code, what the compiler can guarantee, and how easily another shape could be added.

## The stages in the commit history

The stages below are in chronological order, from the oldest commit to the newest. Each entry describes the design before the commit, what changed, and the software engineering idea to take away.

### 1. Add `.gitattributes`, `.gitignore`, and `README.md`

**Before:** The repository had no project-specific source code or introductory documentation.

**Change:** The commit added Git configuration for line-ending normalization, ignored common Visual Studio user and build files, and created the original minimal README.

**Why it helped:** These files make collaboration more predictable: generated files are less likely to clutter commits, and line endings are normalized across environments.

**Concept:** Repository hygiene and version-control configuration are part of maintaining a software project, even though they do not change application behavior.

### 2. Add project files

**Before:** The repository had configuration and documentation, but no runnable application.

**Change:** A .NET 8 Windows Forms solution was added, including its project, startup code, and an initial form with its designer and resources.

**Why it helped:** This established the application structure and a place to build the examples incrementally.

**Concept:** A working application starts with a framework and entry point. The later shape examples are additions to this scaffold, not standalone programs.

### 3. Example 1 with no inheritance

**Before:** The project contained a basic form but no shape model.

**Change:** `Circle` and `Rect` were introduced as separate classes. Each stored its own position, colour, and dimensions, and each implemented its own drawing and description behavior. The form created and handled circles and rectangles separately.

**Why it helped:** Each shape had a clear home for its own data and drawing logic, but shared details—such as position, colour, and drawing a label—were duplicated.

**Concept:** This is a useful baseline for recognizing duplication. Separate classes work, but changes to common behavior may need to be repeated in every shape class.

### 4. Example 2 with inheritance for shape classes

**Before:** `Circle` and `Rect` independently stored common state and repeated common behavior.

**Change:** A `Shape` base class was added to hold shared colour and position data and common drawing/description behavior. `Circle` and `Rect` inherited from it, passed shared values to the base constructor, and kept their own dimensions and shape-specific drawing.

**Why it helped:** Common implementation moved into one place. A derived shape could reuse that behavior while specializing the parts that differ.

**Concept:** Inheritance models an “is-a” relationship: a circle and rectangle are both shapes. It also enables code reuse and is the foundation for treating different derived objects through a common base type.

### 5. Example 3 made handling code simpler

**Before:** The form held separate collections for circles and rectangles and needed a separate loop and variable for each type.

**Change:** The separate collections were replaced by one `ArrayList` of shapes. The form could retrieve each item as `Shape` and call the shared drawing behavior in one loop.

**Why it helped:** The caller no longer needed to duplicate the same handling flow for every concrete shape type.

**Concept:** This demonstrates polymorphism: code can work through a common base type while the actual derived object supplies its specialized drawing implementation.

### 6. Example 4 making it draw random shapes

**Before:** The form added a small, fixed set of example shapes.

**Change:** The example began generating many shapes with randomized positions, dimensions, and colours. `Square` and `Triangle` were added alongside circles and rectangles, and area calculation was introduced for shapes.

**Why it helped:** The example now exercised the shared handling code across a broader and less predictable collection of shape objects.

**Concept:** This stage illustrates extensibility through polymorphism: new derived types can join the same handling flow. It also exposes a limitation of `ArrayList`: it accepts any object, so an unrelated value can accidentally enter the collection.

### 7. Example 5 restricting what goes into the list of shapes

**Before:** The non-generic `ArrayList` could contain arbitrary values, and retrieving items required casts. The random-shape example even included a string that was not a shape.

**Change:** The collection became `List<Shape>`, the invalid string was removed, and handling code no longer needed to test for a circle-only method.

**Why it helped:** The collection now expresses its intended contents. The compiler rejects values that are not shapes, reducing runtime casting errors and making the code easier to understand.

**Concept:** Generic collections provide compile-time type safety. A well-chosen type communicates and enforces an invariant: every item in this list can be handled as a `Shape`.

### 8. Example 5 improving the drawing

**Before:** The form drew every shape directly whenever its paint handler ran. Windows may request repainting more than once.

**Change:** The form began drawing the shapes to a bitmap when it initialized. The paint handler then displayed that prepared bitmap.

**Why it helped:** Repainting the form no longer required repeating the work of drawing every shape. The paint handler became simpler and could restore the already-rendered image.

**Concept:** This is a basic caching/rendering strategy: do expensive or repeatable preparation once, then reuse the result when the UI needs to repaint.

### 9. Example 6 abstract methods and classes

**Before:** `Shape` could be instantiated even though a general shape does not have enough information to define a meaningful area. Its default area method returned `0`, which could conceal a missing implementation.

**Change:** `Shape` became abstract, and `calcArea()` became an abstract method. Concrete shape classes provide their own area calculations.

**Why it helped:** The design prevents incomplete generic shapes from being created and requires each concrete shape to supply its area behavior.

**Concept:** Abstract classes can share state and implemented behavior while leaving essential operations to subclasses. Abstract methods turn that expectation into a compiler-checked obligation.

### 10. Example 7 Added an Interface

**Before:** The abstract `Shape` base class was the shared way to refer to objects that could be drawn and have their area calculated.

**Change:** The `IShape` interface was added with `draw(Graphics)` and `calcArea()` members, and the abstract `Shape` class implemented it.

**Why it helped:** The required shape operations are now stated as a contract, separately from the shared implementation and state provided by `Shape`.

**Concept:** Interfaces describe capabilities without prescribing a class hierarchy. This example uses both ideas together: inheritance shares implementation, while the interface names the behavior a shape promises. The current form still stores `List<Shape>`; the interface establishes a contract that other implementations could also adopt.

## What to look for in the final code

- `Shape` contains shared position and colour state, common drawing behavior, and an abstract area operation.
- `Circle`, `Square`, `Rect`, and `Triangle` provide shape-specific behavior.
- `Form1` creates a `List<Shape>`, draws the shapes through the common type, and renders the result to a bitmap.
- `IShape` declares the drawing and area operations independently of the `Shape` class.

The application targets **.NET 8 on Windows** and uses Windows Forms. Open `InheritanceExample.sln` in Visual Studio to explore or run it. To make the most of the history, compare each commit with the one immediately before it and ask what complexity the change removes—and what new guarantee or flexibility it adds.
