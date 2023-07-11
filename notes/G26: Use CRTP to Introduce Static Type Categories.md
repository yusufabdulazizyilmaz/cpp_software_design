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
