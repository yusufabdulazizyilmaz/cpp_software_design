# A Motivation for CRTP
In high-
performance computing (HPC), any kind of
conditional or indirection, and this includes virtual
functions, is banned from the most performance-critical
parts, such as the innermost loops of compute kernels.
```cpp
template <typename T>
class DynamicVector
{
public:
	using value_type = T;
	using iterator = typename std::vector<T>::iterator;
	using const_iterator = typename std::vector<T>::const_iterator;
	size_t size() const;
	T &operator[](size_t index);
	T const &operator[](size_t index) const;
	iterator begin();
	const_iterator begin() const;
	iterator end();
	const_iterator end() const;

private:
	std::vector<T> values_;
};
template <typename T>
std::ostream &operator<<(std::ostream &os, DynamicVector const<T> &vector)
{
	os << "(";
	for (auto const &element : vector)
	{
		os << " " << element;
	}
	os << " )";
	return os;
}
template <typename T>
auto l2norm(DynamicVector const<T> &vector)
{
	using std::begin, std::end;
	return std::sqrt(std::inner_product(begin(vector),
										end(vector), begin(vector), T{}));
}



template <typename T, size_t Size>
class StaticVector
{
public:
	using value_type = T;
	using iterator = typename std::array<T, Size>::iterator;
	using const_iterator = typename std::array<T, Size>::const_iterator;
	size_t size() const;
	T &operator[](size_t index);
	T const &operator[](size_t index) const;
	iterator begin();
	const_iterator begin() const;
	iterator end();
	const_iterator end() const;

private:
	std::array<T, Size> values_;
};
template <typename T, size_t Size>
std::ostream &operator<<(std::ostream &os,
						 StaticVector<T, Size> const &vector)
{
	os << "(";
	for (auto const &element : vector)
	{
		os << " " << element;
	}
	os << " )";
	return os;
}
template <typename T, size_t Size>
auto l2norm(StaticVector<T, Size> const &vector)
{
	using std::begin, std::end;
	return std::sqrt(std::inner_product(begin(vector),
										end(vector), begin(vector), T{}));
}
```
The Dynamic part of the name implies that itallocates its elements of type T dynamically, in this
example, in the form of std::vector ( ). For that reason, it
is suited for large LA problems (definitely in the range of
several million elements).

The StaticVector class provides the same interface as the
DynamicVector. However, there is an important, performance-related difference
between the two vector classes: as the Static in the name
suggests, the StaticVector does not allocate its elements
dynamically. Instead, it uses an in-class buffer to store its
elements, for instance, with a std::array ( ). Thus, in
contrast to DynamicVector, the entire functionality of
StaticVector is optimized for a small, fixed number of
elements, such as 2D or 3D vectors.

If you take a close look at the associated
output operator of the two vector classes, you will find that
the implementation of these two functions is identical. This is a violation of the Don’t Repeat Yourself (DRY)
principle: it’s easy to forget or miss updating one of the
many places. 

Solution 1: more general function template
```cpp
template <typename DenseVector>
std::ostream &operator<<(std::ostream &os, DenseVector const &vector)
{
	os << "(";
	for (auto const &element : vector)
	{
		os << " " << element;
	}
	os << " )";
	return os;
}
```
the name of the function template may suggest that it’s
written for only dense vectors (including DynamicVector
and StaticVector), but this function template will in fact
accept any type: DynamicVector, StaticVector,
std::vector, std::string, and fundamental types such as
int and double. It simply fails to specify any requirement
or any kind of constraint.
**Specify concepts for all template arguments.**

**What we need is a totally new set of types, a new type
category.**
Solution 2: base class
```cpp
template <typename T> // Type of the elements
class DenseVector
{
public:
	virtual ~DenseVector() = default;
	virtual size_t size() const = 0;
	virtual T &operator[](size_t index) = 0;
	virtual T const &operator[](size_t index) const = 0;
	// ...
};
template <typename T>
std::ostream &operator<<(std::ostream &os, DenseVector<T> const &vector)
{
	// ... as before
}
```
Problems:  
* how to declare the begin() and end() functions.
* how to abstract from different iterator types, such as std::vector<T>::iterator
* turn all our member functions into virtual member functions
# The CRTP Design Pattern Explained
**Intent: “Define a compile-time abstraction for a family of related
types.”**  
The CRTP design pattern builds on the common idea of
creating an abstraction using a base class.(class template). But instead of
establishing a runtime relationship between base and
derived classes via virtual functions, it creates a compile-
time relationship.
```cpp
template< typename Derived >
struct DenseVector
{
   constexpr Derived&       derived()       noexcept { return static_cast<Derived&>(*this); }
   constexpr Derived const& derived() const noexcept { return static_cast<Derived const&>(*this); }

   constexpr size_t size() const noexcept { return derived().size(); }

   decltype(auto) operator[]( size_t index )       noexcept { return derived()[index]; }
   decltype(auto) operator[]( size_t index ) const noexcept { return derived()[index]; }

   decltype(auto) begin()       noexcept { return derived().begin(); }
   decltype(auto) begin() const noexcept { return derived().begin(); }
   decltype(auto) end()         noexcept { return derived().end(); }
   decltype(auto) end()   const noexcept { return derived().end(); }
protected:
   ~DenseVector() = default;	
};

template< typename Derived >
std::ostream& operator<<( std::ostream& os, DenseVector<Derived> const& vector )
{
   size_t const size( vector.size() );

   os << "(";
   for( size_t i=0UL; i<size; ++i ) {
      os << " " << vector[i];
   }
   os << " )";

   return os;
}

template< typename Derived >
decltype(auto) l2norm( DenseVector<Derived> const& vector )
{
   using T = typename Derived::value_type;
   return std::sqrt( std::inner_product( std::begin(vector), std::end(vector)
                                       , std::begin(vector), T{} ) );
}
template< typename T >
class DynamicVector  : public DenseVector< DynamicVector<T> >
{};

template< typename T, size_t Size >
class StaticVector   : public DenseVector< StaticVector<T,Size> >
{};
```
* Since we want to avoid any virtual functions, we’re also not
interested in a virtual destructor. Therefore, we implement
the destructor as a nonvirtual function in the protected
section of the class.
* There is no way that you can use the
nested types of the derived class for the return types of the
CRTP class. decltype(auto)
# Analyzing the Shortcomings of the CRTP Design Pattern
* There is no common base class!
Thus, whenever a common base class is required, a
common abstraction that can be used, for instance, to store
different types in a collection, the CRTP design pattern is
not the right choice
* Everything that comes in touch with a CRTP base class
becomes a template itself.
* CRTP is an intrusive design pattern.
* CRTP does not provide runtime
polymorphism, only compile-time polymorphism. Therefore,
the pattern makes sense only if some kind of static type
abstraction is required.
# The Future of CRTP: A Comparison Between CRTP and C++20 Concepts
```cpp
struct DenseVectorTag {};

template< typename T >
struct IsDenseVector
   : public std::is_base_of<DenseVectorTag,T>
{};

template< typename T >
constexpr bool IsDenseVector_v = IsDenseVector<T>::value;

template< typename T >
concept DenseVector =
   requires ( T t, size_t index ) {
      t.size();
      t[index];
      { t.begin() } -> std::same_as<typename T::iterator>;
      { t.end() } -> std::same_as<typename T::iterator>;
   } &&
   requires ( T const t, size_t index ) {
      t[index];
      { t.begin() } -> std::same_as<typename T::const_iterator>;
      { t.end() } -> std::same_as<typename T::const_iterator>;
   } &&
   IsDenseVector_v<T>;

template< DenseVector VectorT >
std::ostream& operator<<( std::ostream& os, VectorT const& vector )
{
   size_t const size( vector.size() );

   os << "(";
   for( size_t i=0UL; i<size; ++i ) {
      os << " " << vector[i];
   }
   os << " )";

   return os;
}

template< DenseVector VectorT >
decltype(auto) l2norm( VectorT const& vector )
{
   using T = typename VectorT::value_type;
   return std::sqrt( std::inner_product( std::begin(vector), std::end(vector)
                                       , std::begin(vector), T{} ) );
}

template< typename T >
class DynamicVector : private DenseVectorTag
{};


template< typename T, size_t Size >
class StaticVector
{};
// specialization of IsDenseVector
template< typename T, size_t Size >
struct IsDenseVector< StaticVector<T,Size> >
   : public std::true_type
{};
```
