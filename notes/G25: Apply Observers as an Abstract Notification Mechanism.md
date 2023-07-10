**Intent: “Define a one-to-many dependency between objects so that
when one object changes state, all its dependents are notified and
updated automatically.**

![UML Observer](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/the%20UML%20of%20Observer.png)

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
  
 
