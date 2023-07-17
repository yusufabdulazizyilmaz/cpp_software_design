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
