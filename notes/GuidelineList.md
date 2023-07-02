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
* 
# Guideline 18: Beware the Performance of Acyclic Visitor
# Guideline 19: Use Strategy to Isolate How Things Are Done
# Guideline 20: Favor Composition over Inheritance
# Guideline 21: Use Command to Isolate What Things Are Done
# Guideline 22: Prefer Value Semantics over Reference Semantics
# Guideline 23: Prefer a Value-Based Implementation of Strategy and Command
# Guideline 24: Use Adapters to Standardize Interfaces
# Guideline 25: Apply Observers as an Abstract Notification Mechanism
# Guideline 26: Use CRTP to Introduce Static Type Categories
# Guideline 27: Use CRTP for Static Mixin Classes
# Guideline 28: Build Bridges to Remove Physical Dependencies
# Guideline 29: Be Aware of Bridge Performance Gains and Losses
# Guideline 30: Apply Prototype for Abstract Copy Operations
# Guideline 31: Use External Polymorphism for Nonintrusive Runtime Polymorphism
# Guideline 32: Consider Replacing Inheritance Hierarchies with Type Erasure
# Guideline 33: Be Aware of the Optimization Potential of Type Erasure
# Guideline 34: Be Aware of the Setup Costs of Owning Type Erasure Wrappers
# Guideline 35: Use Decorators to Add Customization Hierarchically
# Guideline 36: Understand the Tradeoff Between Runtime and Compile Time Abstraction
# Guideline 37: Treat Singleton as an Implementation Pattern, Not a Design Pattern
# Guideline 38: Design Singletons for Change and Testability
# Guideline 39: Continue to Learn About Design Patterns
