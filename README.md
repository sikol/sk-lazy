# `sk::error`

`sk:error` is a simple container class for passing exceptions by value.  Its 
primary purpose is to allow exceptions to be returned by value in code paths
where exceptions are undesirable.  This allows exception-free code to re-use
existing exception hierarchies to describe error conditions.

Note that `sk:error` itself is not guaranteed to be exception free (for 
example, it can throw `std::bad_alloc` during construction) but it does not
throw exceptions during normal operation.

## Interface reference

The `sk:error` object is copyable and movable, and can be constructed from
any object that derives from `std::exception`.  

* `auto what() const -> char const *`: Return the stored exception's `what()`.
* `operator bool() const`: Return `true` if the error object is empty (i.e.,
  indicates success and does not contain an exception object), or `false` if
  an error condition is present.
* `auto is<T>() const -> bool`: Return `true` if the error is non-empty and
  contains an exception type which is or derives from `T`.
* `auto get<T>() const -> T const *`: If `is<T>()`, return the value of
  the stored exception.

## Installation

`sk::error` is a single-header library, so you can simply drop the header
file into your source tree.  If your project uses CMake, you can add the 
entire project as a subdirectory (e.g., with Git submodules) and link with
the `sk-error` library.

## Sample usage

```c++
sk::error print_non_empty_string(char const *str) {
	if (!*str)
		return std::domain_error("string cannot be empty")
	std::cout << str << '\n';
	return sk::error();
}

int main(int argc, char **argv) {
	auto err = print_non_empty_string(argv[1]);
	if (!err)
		std::cerr << "could not print the string: " << err.what() << '\n';
	return 0;
}
```

Errors can be tested for type (requires RTTI):

```c++
	auto err = f();
	if (err.is<std::domain_error>())
		std::cout << "domain error\n";
```

The base exception type can be retrieved (requires RTTI):

```c++
	auto err = f();
	std::domain_error *domerr = err.get<std::domain_error>();
```

`sk::error` works well with "expected" / "outcome" interfaces:

```c++
expected<int, sk::error> unhelpful_abs(int i) {
	if (i < 0)
		return -i;

	return make_unexpected(std::domain_error("integer must be negative"));
}
```
