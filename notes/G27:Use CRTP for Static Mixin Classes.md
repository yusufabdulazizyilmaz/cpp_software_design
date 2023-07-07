# Mixin Classes: The Yang of the CRTP

Mixin classes are template classes that define a generic behaviour, and are designed to inherit from the type 
you wish to plug their functionality onto.

```cpp
class Name
{
public:
	Name(std::string firstName, std::string lastName)
		: firstName_(std::move(firstName)), lastName_(std::move(lastName)) {}

	void print() const
	{
		std::cout << lastName_ << ", " << firstName_ << '\n';
	}

private:
	std::string firstName_;
	std::string lastName_;
};

// Mixin classes are template classes that define a generic behaviour,
// and are designed to inherit from the type you wish
// to plug their functionality onto.

template <typename Printable>
struct RepeatPrint : Printable
{
	explicit RepeatPrint(const Printable &pr) : Printable(pr)
	{
	}
	void repeat(unsigned int n) const
	{
		while (n-- > 0)
			this->print();
	}
};
int main()
{
	Name ned("Eddard", "Stark");
	RepeatPrint{ned}.repeat(5);
	return 0;
}
```
* leaves the original class unchanged,
* client code doesn’t use the original class directly, it needs to wrap it into the mixin to use the augmented functionality,
* inherits from a the original class even if it doesn’t have a virtual destructor. This is ok unless the mixin class is deleted polymorphically through a pointer to the original class.
```cpp
template <typename Derived>
struct RepeatPrint
{
	void repeat(unsigned int n) const
	{
		while (n-- > 0)
			static_cast<const Derived *>(this)->print();
	}
};

class Name : public RepeatPrint<Name>
{
public:
	Name(std::string firstName, std::string lastName)
		: firstName_(std::move(firstName)), lastName_(std::move(lastName)) {}

	void print() const
	{
		std::cout << lastName_ << ", " << firstName_ << '\n';
	}

private:
	std::string firstName_;
	std::string lastName_;
};
int main()
{
	Name ned("Eddard", "Stark");
	ned.repeat(2);
	return 0;
}
```
* impacts the definition of the existing class, because it has to inherit from the CRTP,
* client code uses the original class directly and benefits from its augmented functionalities.

we provide the operations via CRTP mixin classes: base classes that “inject” the desired
operations. Our solution of choice is to provide the mixins in the form of optional template arguments.
