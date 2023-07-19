# The External Polymorphism Design Pattern Explained
**Intent: “Allow C++ classes unrelated by inheritance and/or having no
virtual methods to be treated polymorphically. These unrelated
classes can be treated in a common manner by software that uses
them.”**

we can extract the complete polymorphic behavior with the
External Polymorphism design pattern.
![UML](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/UML%20of%20External%20Polymorphism.png)
* there is no Shape base class anymore.
* the shapes are not expected to know anything about drawing.
* introduces a separate inheritance hierarchy in then form of the ShapeConcept and ShapeModel classes.
* set of virtual functions has been moved into the abstract ShapeConcept class.
* concrete shapes are not required to know about ShapeConcept and, in particular, are not expected to
inherit from it.
* ShapeModel class template  is instantiated for a specific kind of shape (Circle, Square, etc.) and acts as a wrapper
for it.
* ShapeModel does not implement the logic of the virtual functions itself but delegates the request to the desired
implementation.
* a very compelling combination of inheritance (runtime) and templates(compile-time).
