# Example code

- [various examples 1](#various-examples-1)
- [smallest scope](#keep-scope-as-limited-as-possible)
- [write testable code](#write-testable-code-that-reads-like-english)

## various examples 1

```cpp
#include <string>

// applies: explicit, sink, [[nodiscard]], const
class Example
{
public:
    explicit Example(std::string name) : m_name(std::move(name))
    {
    }

    [[nodiscard]] std::string get_name() const
    {
        return m_name;
    }

    void set_name(std::string name)
    {
        m_name = std::move(name);
    }

private:
    std::string m_name;
};
```

## Keep scope as limited as possible

Instead of this:

```cpp
void example(bool condition)
{
    int count = 0;

    // more code
    
    if (condition)
    {
        count = 42;
    }
    return count;
}
```

Do this:

```cpp

void example(bool condition)
{
    // more code
    
    int count = 0;  // count moved here, just before it is needed
    if (condition)
    {
        count = 42;
    }
    return count;
}
```

Or prevent using a temporary at all:

```cpp

void example(bool condition)
{
    // more code
    
    if (condition)
    {
        return 42;
    }
    return 0;
}
```


## write testable code that reads like English

[godbolt](https://cppcoach.godbolt.org/z/xKoWzM8q6)

```cpp
#include <iostream>
#include <vector>
// drawbacks:
// - this function does several things (no single responsibility)
// - variables are in scope until the end, easy to use incorrectly
// - comments needed to explain the steps
// - no bounds checking
// - unit testing not possible
void instead_of_this() 
{
    std::vector<int> values = {1, 2, 3, 4, 5};
    int sum = 0;
    double average = 0.0;

    // Calculate sum
    for (size_t i = 0; i < values.size(); ++i) {
        sum += values[i];
    }

    // Calculate average
    average = static_cast<double>(sum) / double(values.size());

    // Print values greater than average
    for (size_t i = 0; i < values.size(); ++i) {
        if (values[i] > average) {
            std::cout << values[i] << " ";
        }
    }
    std::cout << '\n';
}

////////////////////////////////////////////////////////

#include <algorithm>
#include <numeric>
#include <print>
#include <vector>

std::vector<int> get_values_greater_than(const std::vector<int>& values, int threshold) {
    std::vector<int> result;
    std::copy_if(values.begin(), values.end(), std::back_inserter(result),
                 [threshold](int value) { return value > threshold; });
    return result;
}

int calculate_sum(const std::vector<int> & values) {
    return std::accumulate(values.begin(), values.end(), 0);
}

int calculate_average(const std::vector<int> & values) {
    return calculate_sum(values) / int(values.size());
}

void print_values(const std::vector<int> &values) {
    for (const auto value: values) {
        std::print("{} ", value);
    }
    std::println("");
}

// advantages:
// - code reads (almost) like normal english, no comments needed
// - guaranteed to never read out of bounds
// - steps are separately testable by unit tests

void do_this() {
    std::vector<int> values = {1, 2, 3, 4, 5};
    const int average = calculate_average(values);
    print_values(get_values_greater_than(values, average));
}

int main()
{
    instead_of_this();
    do_this();
    return 0;
}
```