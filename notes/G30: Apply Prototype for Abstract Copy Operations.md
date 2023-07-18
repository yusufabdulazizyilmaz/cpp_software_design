# The Prototype Design Pattern Explained
**Intent: “Specify the kind of objects to create using a prototypical
instance, and create new objects by copying this prototype.”**
![UML](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/the%20UML%20of%20Prototype.png)
```cpp
//---- <Animal.h> ---------------------------------------------------------------------------------

#include <memory>

class Animal
{
 public:
   virtual ~Animal() = default;
   virtual void makeSound() const = 0;
   virtual std::unique_ptr<Animal> clone() const = 0; // Prototype design pattern
};


//---- <Sheep.h> ----------------------------------------------------------------------------------

//#include <Animal.h>
#include <string>

class Sheep : public Animal
{
 public:
   explicit Sheep( std::string name ) : name_{ std::move(name) } {}

   void makeSound() const override;
   std::unique_ptr<Animal> clone() const override;  // Prototype design pattern

 private:
   std::string name_;
};


//---- <Sheep.cpp> --------------------------------------------------------------------------------

//#include <Sheep.h>
#include <iostream>

void Sheep::makeSound() const
{
   std::cout << "baa\n";
}

std::unique_ptr<Animal> Sheep::clone() const
{
   return std::make_unique<Sheep>(*this);  // Copy-construct a sheep
}
```
* Sheep class neither deletes nor hides its copy
constructor and copy assignment operator. Hence, if you
have a sheep, you can still copy the sheep with the special
member functions.
* the clone() merely adds one more way to create a copy—a way to
perform virtual copying.
