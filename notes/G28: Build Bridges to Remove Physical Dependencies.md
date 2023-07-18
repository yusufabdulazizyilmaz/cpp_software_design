# A Motivating Example
```cpp
//---- <ElectricEngine.h> ----------------
class ElectricEngine
{
    void start();
    void stop();
};
//---- <ElectricCar.h> ----------------
#include <ElectricEngine.h>
// ...
class ElectricCar
{
public:
	ElectricCar(/*maybe some engine arguments*/);

private:
	ElectricEngine engine_;
};

//---- <ElectricCar.cpp> ----------------
#include <ElectricCar.h>
ElectricCar::ElectricCar(/*maybe some engine arguments*/)
	: engine_{/*engine arguments*/}
{}
```
* Easily results in transitive, physical coupling: every file that includes the <ElectricCar.h>
header will physically depend on the <ElectricEngine.h> header.
* This design reveals all implementation details to everyone. The private label is merely an access label. It
is not a visibility label. Therefore, everything in your class definition (and I mean everything) is visible to everyone
who sees the ElectricCar class definition.
* You cannot change the implementation details of this class
without anyone noticing. In particular, this may be aproblem if you need to provide ABI stability, i.e., if the in-
memory representation of your class must not change.
```cpp
//---- <ElectricCar.h> ----------------
#include <memory>
struct ElectricEngine; // Forward declaration
class ElectricCar
{
public:
	ElectricCar(/*maybe some engine arguments*/);

private:
	std::unique_ptr<ElectricEngine> engine_;
};

//---- <ElectricCar.cpp> ----------------
#include <ElectricCar.h>
#include <ElectricEngine.h>
ElectricCar::ElectricCar(/*maybe some engine arguments*/)
	: engine_{std::make_unique<ElectricEngine>(/*engine arguments*/)}
{}
```
* the physical dependency is gone, since the <ElectricEngine.h> header has been moved into the source file.
* still remains is the visibility of the implementation details.
* any change to these details, such as an upgrade to the new PowerEngine, would
affect any class that works with the <ElectricCar.h> header file.

To get rid of this dependency and gain the luxury
of being able to easily change the implementation details at
any time without anyone noticing, we have to introduce an
abstraction. The classic form of abstraction is the
introduction of an abstract class:
```cpp
//---- <Engine.h> ----------------
class Engine
{
public:
	virtual ~Engine() = default;
	virtual void start() = 0;
	virtual void stop() = 0;
};
//---- <ElectricEngine.h> ----------------
#include <Engine.h>
class ElectricEngine : public Engine
{
public:
	void start() override;
	void stop() override;
};
//---- <ElectricCar.h> ----------------
#include <Engine.h>
#include <memory>
class ElectricCar
{
private:
	std::unique_ptr<Engine> engine_;
};

//---- <ElectricCar.cpp> ----------------
#include <ElectricCar.h>
#include <ElectricEngine.h>
ElectricCar::ElectricCar(/*maybe some engine arguments*/)
	: engine_{std::make_unique<ElectricEngine>(/*engine arguments*/)}
{}
```
* We can easily change the implementation details at any time by only modifying the source file
# The Bridge Design Pattern Explained
**Intent: “Decouple an abstraction from its implementation so that the
two can vary independently.”**
![Earth](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/UML%20of%20the%20Bridge.png)
```cpp
//---- <Car.h> ----------------
#include <Engine.h>
#include <memory>
#include <utility>
class Car
{
protected:
	explicit Car(std::unique_ptr<Engine> engine)
		: pimpl_(std::move(engine))
	{
	}

public:
	virtual ~Car() = default;
	virtual void drive() = 0;

protected:
	Engine *getEngine() { return pimpl_.get(); }
	Engine const *getEngine() const { return pimpl_.get(); }

private:
	std::unique_ptr<Engine> pimpl_; // Pointer-to-implementation (pimpl)
};

//---- <ElectricCar.h> ----------------
#include <Engine.h>
#include <memory>
class ElectricCar : public Car
{
public:
	explicit ElectricCar(/*maybe some engine arguments*/);
	void drive() override;
};

//---- <ElectricCar.cpp> ----------------
#include <ElectricCar.h>
#include <ElectricEngine.h>
ElectricCar::ElectricCar(/*maybe some engine arguments*/)
	: Car(std::make_unique<ElectricEngine>(/*engine arguments*/))
{
}
```
# The Pimpl Idiom
local, nonpolymorphic form of Bridge is called the Pimpl idiom.
```cpp
//---- <Person.h> ---------------------------------------------------------------------------------

#include <memory>

class Person
{
public:
	// ...
	Person();
	~Person();

	Person(Person const &other);
	Person &operator=(Person const &other);

	Person(Person &&other);
	Person &operator=(Person &&other);

	int year_of_birth() const;
	// ... Many more access functions

private:
	struct Impl;
	std::unique_ptr<Impl> const pimpl_;
};

//---- <Person.cpp> -------------------------------------------------------------------------------

// #include <Person.h>
#include <string>

struct Person::Impl
{
	std::string forename;
	std::string surname;
	std::string address;
	std::string city;
	std::string country;
	std::string zip;
	int year_of_birth;
	// ... Potentially many more data members
};

Person::Person()
	: pimpl_{std::make_unique<Impl>()}
{
}

Person::~Person() = default;

Person::Person(Person const &other)
	: pimpl_{std::make_unique<Impl>(*other.pimpl_)}
{
}

Person &Person::operator=(Person const &other)
{
	*pimpl_ = *other.pimpl_;
	return *this;
}

Person::Person(Person &&other)
	: pimpl_{std::make_unique<Impl>(std::move(*other.pimpl_))}
{
}

Person &Person::operator=(Person &&other)
{
	*pimpl_ = std::move(*other.pimpl_);
	return *this;
}

int Person::year_of_birth() const
{
	return pimpl_->year_of_birth;
}
```
* All you have to do is move the definition of the Person destructor to the source file.
* implemented under the assumption that every instance ofPerson will always have a valid pimpl_.
* year_of_birth() member functions imple getter function is a
great inline candidate, the definition has to be moved to
the source file. The reason is that in the header file, Impl is
an incomplete type. Which means that within the header
file, you are not able to access any members (both data and functions).
# Comparison Between Bridge and Strategy
* data member is initialized is a strong indicator about which one you’re using.
* If a class does not want to know about some implementation detail, it provides the
opportunity to configure the behavior by passing in details
from the outside (for instance, via a constructor or via a setter function) Strategy design pattern
* If, however, a class knows about the implementation details
but primarily wants to reduce the physical dependencies on
these details the pointer is an implementation detail and set internally Bridge design pattern
```cpp
// Strategy Design Pattern
class DatabaseEngine
{
public:
	virtual ~DatabaseEngine() = default;
};
class Database
{
public:
	explicit Database(std::unique_ptr<DatabaseEngine> engine);

private:
	std::unique_ptr<DatabaseEngine> engine_;
};
// The database is unaware of any implementation details and requests
// them via its constructor from outside -> Strategy design pattern
Database::Database(std::unique_ptr<DatabaseEngine> engine)
	: engine_{std::move(engine)}
{}
// Bridge Design Pattern
class Database
{
public:
	explicit Database();
	// ...
private:
	std::unique_ptr<DatabaseEngine> pimpl_;
};
// The database knows about the required implementation details,
// but does not want to depend too strongly on it
Database::Database()
	: pimpl_{std::make_unique<ConcreteDatabaseEngine>(/*somearguments*/)}
{}
```
# Analyzing the Shortcomings of the Bridge Design Pattern
* Bridge introduces an additional indirection: the pimpl pointer making all access to the implementation details
more expensive.
* you will have to pay for an additional dynamic memory allocation.
* memory overhead caused by introducing the pimpl pointer
* the code complexity has increased.
