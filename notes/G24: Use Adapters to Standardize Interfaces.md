**Intent: “Convert the interface of a class into another interface clients
expect. Adapter lets classes work together that couldn’t otherwise
because of incompatible interfaces.”**
![Adapter](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/the%20UML%20of%20Adapter.png)

# Object Adapters Versus Class Adapters
* object adapter: you store **an instance** of the wrapped type or store **a pointer
to the base class** of an inheritance hierarchy.
```cpp
class Pages : public Document
{
public:
	// ...
	void exportToJSON(/*...*/) const override
	{
		exportToJSONFormat(pages, /*...*/);
	}
	void serialize(ByteStream &bs, /*...*/) const override
	{
		pages.convertToBytes(/*...*/);
	}
	// ...
private:
	OpenPages pages; // Example of an object adapter
};
```
* class adapter: you would inherit from it (if possible, nonpublicly) and implement the expected
interface accordingly.
```cpp
class Pages : public Document, private OpenPages // Example of a class adapter
{
public:
	// ...
	void exportToJSON(/*...*/) const override
	{
		exportToJSONFormat(*this, /*...*/);
	}
	void serialize(ByteStream &bs, /*...*/) const override
	{
		this->convertToBytes(/*...*/);
	}
	// ...
};
```
There are only a few reasons why you would prefer a class adapter:
* If you have to override a virtual function.
* If you need access to a protected member function.
* If you require the adapted type to be constructed
before another base class.
* If you need to share a common virtual base class or
override the construction of a virtual base class.
* If you can draw significant advantage from the Empty
Base Optimization (EBO).

While I do see the advantages of the name PagesAdapter,
as this immediately communicates that you built on the
Adapter design pattern, I don’t consider it a necessity to
communicate the fact that this class represents an adapter.To me, the Adapter feels like an implementation detail in
this situation: I do not need to know that the Pages class
doesn’t implement all the details itself, but uses the
OpenPages class for that.

# Analyzing the Shortcomings of the Adapter Design Pattern
it’s very important that you consider the expected behavior and check for LSP violations when
applying this design pattern:
```cpp
//---- <TurkeyAdapter.h> ----------------
#include <memory>
class TurkeyAdapter : public Duck
{
public:
	explicit TurkeyAdapter(std::unique_ptr<Turkey> turkey)
		: turkey_{std::move(turkey)}
	{}
	void quack() override { turkey_->gobble(); }
	void fly() override { turkey_->fly(); }

private:
	std::unique_ptr<Turkey> turkey_; // This is an example for an object adapter
};
```

