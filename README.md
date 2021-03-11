# `sk::lazy`

`sk::lazy<T>` wraps a callable that returns `T` and caches the result
after the first call.

## Sample usage

```c++
extern int slow_function();

int main() {
	sk::lazy<T> lz(slow_function);

	// Call slow_function() and cache the result.
	std::cout << "the result of slow_function is " << lz.get() << '\n';

	// This call will be fast because the result is cached.
	std::cout << "the result of slow_function is still " << lz.get() << '\n';
}
```
