# The Type Erasure Design Pattern Explained
**Intent: “Provide a value-based, non-intrusive abstraction for an
extendable set of unrelated, potentially non-polymorphic types with
the same semantic behavior.”**
![DependencyGraph](https://github.com/yusufabdulazizyilmaz/cpp_software_design/blob/main/notes/DependencyGraph%20of%20Type%20Erasure.png)

Type Erasure is nothing but a compound design pattern External Polymorphism + Bridge + Prototype


```cpp
namespace detail {
class ShapeConcept
{
public:
	virtual std::unique_ptr<ShapeConcept> clone() const = 0;
};

template <typename ShapeT, typename DrawStrategy>
class OwningShapeModel : public ShapeConcept
{
public:
	std::unique_ptr<ShapeConcept> clone() const override
	{
		return std::make_unique<OwningShapeModel>(*this);
	}
};
} // namespace detail
//---- <Shape.h> ----------------
class Shape
{
public:
	template <typename ShapeT, typename DrawStrategy>
	Shape(ShapeT shape, DrawStrategy drawer)
	{
		using Model = detail::OwningShapeModel<ShapeT, DrawStrategy>;
		pimpl_ = std::make_unique<Model>(std::move(shape), std::move(drawer));
	}

	Shape(Shape const &other)
		: pimpl_(other.pimpl_->clone())
	{
	}
	Shape &operator=(Shape const &other)
	{
		// Copy-and-Swap Idiom
		Shape copy(other);
		pimpl_.swap(copy.pimpl_);
		return *this;
	}
	~Shape() = default;
	Shape(Shape &&) = default;
	Shape &operator=(Shape &&) = default;

private:
	friend void draw(Shape const &shape)
	{
		shape.pimpl_->draw();
	}

	std::unique_ptr<detail::ShapeConcept> pimpl_;
};
```
* (External Polymorphism) ShapeConcept and OwningShapeModel moved to the detail namespace two classes are now
becoming implementation details, i.e., they are not intended for direct use anymore.
* 
