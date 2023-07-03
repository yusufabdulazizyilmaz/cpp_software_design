# Guideline 1: Understand the Importance of Software Design
* Focus less on C++ language details and more on software design.
* Understand software design as the art of managing dependencies and abstractions.
# Guideline 2: Design for Change
* Adhere to the Single-Responsibility Principle (SRP) to separate concerns.
* Follow the Don’t Repeat Yourself (DRY) principle to minimize duplication.
# Guideline 3: Separate Interfaces to Avoid Artificial Coupling
* Adhere to the Interface Segregation Principle (ISP) to separate concerns in interfaces.
* Consider the ISP as a special case of the Single-Responsibility Principle (SRP).
# Guideline 4: Design for Testability
* Consider private member functions that need testing to be misplaced.
* Prefer nonmember non-friend functions to member functions.
# Guideline 5: Design for Extension
* Adhere to the Open-Closed Principle (OCP) to keep code open for extension but closed for modification.
* Design for code additions by means of base classes, templates, function overloading, or template specialization.
# Guideline 6: Adhere to the Expected Behavior of Abstractions
* Understand that an abstraction represents a set of requirements and expectations.
* Follow the Liskov Substitution Principle (LSP) to adhere to the expected behavior of abstractions.
* Communicate the expectations of an abstraction.
# Guideline 7: Understand the Similarities Between Base Classes and Concepts
* Consider concepts (both the C++20 feature and pre- C++20 named template arguments) as the static equivalent of base classes.
# Guideline 8: Understand the Semantic Requirements of Overload Sets
* Be aware that function overloading is a compile-time abstraction mechanism.
* Keep in mind that there are expectations on the behavior of functions within an overload set.
# Guideline 9: Pay Attention to the Ownership of Abstractions
* Keep in mind that in a proper architecture, low-level implementation details depend on high-level abstractions.
* Adhere to the Dependency Inversion Principle (DIP), and assign abstractions to the high level of an architecture.
* Make sure abstractions are owned by the high level, not by the low level.
# Guideline 10: Consider Creating an Architectural Document
* Understand that an architectural document serves the purpose of maintaining and communicating the current state of the architecture.
# Guideline 11: Understand the Purpose of Design Patterns
* Understand that design patterns are proven, named solutions with an intent to decouple.
* Realize that design patterns introduce some kind of abstraction.
# Guideline 12: Beware of Design Pattern Misconceptions
* Consider design patterns as a tool to solve a design problem, not as a goal.
* Be aware that design patterns are not limited to object-oriented programming.
* Understand that design patterns are not languagespecific idioms.
# Guideline 13: Design Patterns Are Everywhere
* Understand that any kind of abstraction and any attempt to decouple likely represents a known design pattern.
* Apply design patterns based on their intent whenever necessary.
# Guideline 14: Use a Design Pattern’s Name to Communicate Intent
* Use the name of a design pattern to communicate the intent of a solution.
* Use the name of a design pattern to improve readability.
# Guideline 15: Design for the Addition of Types or Operations
* Understand the choice between the addition of types OR operations in dynamic polymorphism.
* Prefer an object-oriented solution when you primarily want to add types.
* Prefer a procedural/functional solution when you primarily want to add operations.
# Guideline 16: Use Visitor to Extend Operations
* Keep in mind that it’s difficult to add a new operation in an existing inheritance hierarchy.
* Apply the Visitor design pattern with the intent of enabling the easy addition of operations.
# Guideline 17: Consider std::variant for Implementing Visitor
* Be aware of the advantages of std::variant in comparison to an object-oriented Visitor solution.
* Keep in mind the shortcomings of std::variant and avoid it when it’s not appropriate.
# Guideline 18: Beware the Performance of Acyclic Visitor
* Understand the architectural advantages of an Acyclic Visitor.
* Be aware of the significant performance disadvantages of that solution.
# Guideline 19: Use Strategy to Isolate How Things Are Done
* Apply the Strategy design pattern with the intent to extract the implementation details of a cohesive set of functions.
* Implement one Strategy for each operation to avoid artificial coupling.
* Consider policy-based design as the compile-time form of the Strategy design pattern.
# Guideline 20: Favor Composition over Inheritance
* Keep in mind that inheritance creates a tight coupling.
* Realize that many design patterns are enabled by composition, not by inheritance.
# Guideline 21: Use Command to Isolate What Things Are Done
* Apply the Command design pattern with the intent to abstract and encapsulate an (possibly undoable) action.
# Guideline 22: Prefer Value Semantics over Reference Semantics
* Be aware that reference semantics make it harder to understand code
* Prefer the semantic clarity of value semantics.
# Guideline 23: Prefer a Value-Based Implementation of Strategy and Command
* Consider using std::function to implement the Strategy or Command design pattern.
* Take the performance disadvantages of std::function into account.
* Be aware that Type Erasure is a generalization of the value semantics approach for Strategy and Command.
# Guideline 24: Use Adapters to Standardize Interfaces
* Apply the Adapter design pattern with the intent to adapt interfaces so that otherwise incompatible pieces can work together.
* Distinguish among object adapters, class adapters, and function adapters.
* Pay attention to LSP violations when using the Adapter design pattern.
# Guideline 25: Apply Observers as an Abstract Notification Mechanism
* Apply the Observer design pattern with the intent to create a one-to-many relationship between a subject and its observers.
* Understand the trade-offs between push observers and pull observers.
* Utilize the advantages of a value semantics–based Observer implementation.
# Guideline 26: Use CRTP to Introduce Static Type Categories
* Apply the CRTP design pattern to define a compile-time abstraction for a family of related types.
* Be aware of the limited access from the CRTP base class to the derived class.
* Keep in mind the restrictions of the CRTP design pattern, in particular, the lack of a common base class.
* Prefer C++20 concepts to the CRTP design pattern when possible.
# Guideline 27: Use CRTP for Static Mixin Classes
* Be aware between the difference between using CRTP as a design pattern and using it as an implementation pattern.
* Understand that CRTP base classes that represent an abstraction act as a design pattern.
* Understand that CRTP base classes that do not represent an abstraction act as an implementation pattern.
# Guideline 28: Build Bridges to Remove Physical Dependencies
* Be aware of physical dependencies introduced by data members or includes.
* Apply the Bridge design pattern with the intent to isolate physical dependencies from implementation details
* Prefer using a pimpl data member to communicate the use of a Bridge.
* Know the difference between reducing physical dependencies (Bridge) and reducing logical dependencies (Strategy).
# Guideline 29: Be Aware of Bridge Performance Gains and Losses
* Be aware that a partial Bridge can have a positive impact on performance when separating frequently used data from infrequently used data.
# Guideline 30: Apply Prototype for Abstract Copy Operations
* Apply the Prototype design pattern with the intent to create copies of abstract entities.
* Prefer building on the two copy operations for value types.
* Keep in mind the performance drawbacks resulting from pointer indirections and memory allocations.
# Guideline 31: Use External Polymorphism for Nonintrusive Runtime Polymorphism
* Apply the External Polymorphism design pattern with the intent to enable the polymorphic treatment of nonpolymorphic types.
* Consider the External Polymorphism design pattern as a key player to achieve loose coupling.
* Exploit the design flexibilities of the externalized inheritance hierarchy.
# Guideline 32: Consider Replacing Inheritance Hierarchies with Type Erasure
* Apply the Type Erasure design pattern with the intent to provide a value-based, nonintrusive
abstraction for an extendable set of unrelated, potentially nonpolymorphic types with the same semantic behavior.
* Consider Type Erasure as a compound design pattern, built from the External Polymorphism,
Bridge, and Prototype design patterns.
* Use the term Type Erasure only to communicate its intent as a design pattern that allows the easy
addition of types supporting a fixed set of operations.
# Guideline 33: Be Aware of the Optimization Potential of Type Erasure
* Use SBO to avoid expensive copy operations for small objects.
* Reduce the number of indirections by implementing virtual dispatch manually.
# Guideline 34: Be Aware of the Setup Costs of Owning Type Erasure Wrappers
* Keep in mind that the setup of owning Type Erasure wrappers may involve copy operations and allocations.
* Be aware of nonowning Type Erasure, but also understand its reference semantics deficiencies.
* Prefer simple Type Erasure implementations, but know their limits.
* Prefer to use nonowning Type Erasure for function arguments but not for data members or return types.
# Guideline 35: Use Decorators to Add Customization Hierarchically
* Apply the Decorator design pattern with the intent
to nonintrusively and hierarchically extend and customize behavior.
* Consider Decorators for combining and reusing
independent pieces of behavior.
# Guideline 36: Understand the Tradeoff Between Runtime and Compile Time Abstraction
* Understand that compile-time solutions usually
perform better but limit runtime flexibility and encapsulation.
* Understand that runtime solutions are more flexible
and are good at hiding details but perform worse.
# Guideline 37: Treat Singleton as an Implementation Pattern, Not a Design Pattern
* The goal of Singleton is not to decouple or manage
dependencies, and thus it does not fulfill the expectations of a design pattern.
* Apply the Singleton pattern with the intent to
restrict the number of instances of a particular class to exactly one.
# Guideline 38: Design Singletons for Change and Testability
* Use Singleton judiciously and just for the few global aspects in your code.
* Prefer Singletons with unidirectional data flow.
* Use the Strategy design pattern to invert dependencies on your Singleton to remove the usual
impediments to changeability and testability.
# Guideline 39: Continue to Learn About Design Patterns
