**Intent: “Define a one-to-many dependency between objects so that
when one object changes state, all its dependents are notified and
updated automatically.**

![UML Observer](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/the%20UML%20of%20Observer.png)

# A Classic Observer Implementation
The most important implementation detail of this class is the pure virtual update() function, 
which is called whenever the observer is notified of some state change.
* push observer: push the updated state via one or even several update() functions:
the observer is given all necessary information by the subject and therefore is not required to pull any information from the subject on its own.
```cpp
class Observer
{
public:
	// ...
	virtual void update1(/*arguments representing the updated state*/) = 0;
	virtual void update2(/*arguments representing the updated state*/) = 0;
	// ...
};
```
* pull observer: the classes deriving from the Observer base class
are required to pull the new information from the subject
on their own.
```cpp
class Observer
{
public:
	// ...
	virtual void update(Subject const &subject) = 0;
	// ...
};
```
* better approach: in addition to passing a reference to the subject,
we pass a tag to provide information about which property
of a subject has changed The tag may help an observer to decide on its own whether
some state change is interesting or not
```cpp
class Observer
{
public:
	virtual ~Observer() = default;
	virtual void update(Subject const &subject, /*Subject-specific type*/ property) = 0;
};
```
# An Observer Implementation Based on Value Semantics
We won’t entirely
stray from the classic implementation: to register and
deregister observers, we will always be in need of some
unique identifier for observers, and the unique address of
an observer is just the easiest and most convenient way to
tackle that problem. Therefore, we’ll stick to using a
pointer to refer to a registered observer. 

Why do we still need the Observer class? Couldn’t we just
directly use std::function? However, as
std::function is a true child of value semantics, we tend
to copy or move std::function objects. But this is not
desirable in this situation: especially if you use a stateful
observer, you don’t want a copy of your observer to be
called

# Analyzing the Shortcomings of the Observer Design Pattern
* the demonstrated std::function approach works well only for a pull
observer with a single update() function.
* there is no pure value-based implementation.
* the potential implementation
issues with Observers: the order of registration and
deregistration may matter a lot, in particular if an observeris allowed to register multiple times. Also, in a multithreaded environment, the thread-safe registration
and deregistration of observers and handling of events are
highly nontrivial topics.
* the overuse of observers can quickly and easily
lead to a complex network of interconnections. Indeed, if
you are not careful, you can accidentally introduce an
infinite loop of callbacks!
 
