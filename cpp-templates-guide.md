# C++ Templates: From Zero to Hero

## Table of Contents

1. [What are Templates?](#what-are-templates)
2. [Function Templates](#function-templates)
3. [Class Templates](#class-templates)
4. [Template Parameters](#template-parameters)
5. [Template Specialization](#template-specialization)
6. [Variadic Templates](#variadic-templates)
7. [SFINAE and Type Traits](#sfinae-and-type-traits)
8. [Template Metaprogramming](#template-metaprogramming)
9. [Modern C++ Features](#modern-c-features)
10. [Advanced Topics](#advanced-topics)
11. [Best Practices](#best-practices)
12. [Common Pitfalls](#common-pitfalls)
13. [Real-World Applications](#real-world-applications)

## What are Templates?

Templates are C++'s way of writing **generic code** - code that works with different types without rewriting it for each type. Think of templates as blueprints that the compiler uses to generate actual code.

**Why use templates?**

- Write code once, use with many types
- Type-safe generic programming
- Zero runtime overhead
- Foundation of modern C++ (STL, smart pointers, etc.)

**Key concept**: Templates are compiled when **instantiated**, not when defined.

## Function Templates

### Your First Template

```cpp
#include <iostream>

// Template function to find maximum of two values
template<typename T>
T max_value(T a, T b) {
    return (a > b) ? a : b;
}

int main() {
    std::cout << max_value(5, 3) << std::endl;        // Works with int
    std::cout << max_value(5.5, 3.2) << std::endl;    // Works with double
    std::cout << max_value('a', 'z') << std::endl;    // Works with char

    return 0;
}
```

### Template Syntax Breakdown

```cpp
template<typename T>  // Template declaration
T function_name(T parameter) {  // T is a placeholder for any type
    // function body
}
```

- `template<typename T>`: Declares a template with type parameter `T`
- `T`: Placeholder for any type (you can use any name, `T` is convention)
- `typename`: Keyword to declare type parameters (can also use `class`)

### Multiple Template Parameters

```cpp
template<typename T, typename U>
auto multiply(T a, U b) -> decltype(a * b) {
    return a * b;
}

int main() {
    auto result1 = multiply(5, 3.14);      // int * double = double
    auto result2 = multiply(2.5, 4);       // double * int = double

    std::cout << result1 << std::endl;     // 15.7
    std::cout << result2 << std::endl;     // 10

    return 0;
}
```
This function uses C++'s trailing return type syntax.
- `auto`: Placeholder for the actual return type
- `decltype(a*b)`: Specifies the actual return type using the decltype operator
`decltype(a*b)` determines the return type based on what type results from multiplying parameters a and b. This allows the function to handle different type combinations automatically:
- When multiplying `int` and `double`, it returns `double`
- When multiplying `int` and `int`, it returns `int`
- When multiplying any types with an `overloaded` * `operator`, it returns whatever type that `operation produces`

### Template Argument Deduction

```cpp
template<typename T>
void print(T value) {
    std::cout << value << std::endl;
}

int main() {
    print(42);          // T deduced as int
    print(3.14);        // T deduced as double
    print("Hello");     // T deduced as const char*

    // Explicit template argument
    print<std::string>("World");  // T explicitly specified as std::string

    return 0;
}
```

### Function Templates with Default Arguments

```cpp
template<typename T = int>
T square(T value) {
    return value * value;
}

int main() {
    auto result1 = square(5);       // Uses default int
    auto result2 = square(2.5);     // T deduced as double
    auto result3 = square<float>(3.0f);  // T explicitly float

    std::cout << result1 << " " << result2 << " " << result3 << std::endl;

    return 0;
}
```

## Class Templates

### Basic Class Template

```cpp
template<typename T>
class Stack {
private:
    std::vector<T> elements;

public:
    void push(const T& element) {
        elements.push_back(element);
    }

    T pop() {
        if (elements.empty()) {
            throw std::runtime_error("Stack is empty");
        }
        T top = elements.back();
        elements.pop_back();
        return top;
    }

    bool empty() const {
        return elements.empty();
    }

    size_t size() const {
        return elements.size();
    }
};

int main() {
    Stack<int> intStack;
    intStack.push(10);
    intStack.push(20);

    std::cout << intStack.pop() << std::endl;  // 20
    std::cout << intStack.pop() << std::endl;  // 10

    Stack<std::string> stringStack;
    stringStack.push("Hello");
    stringStack.push("World");

    std::cout << stringStack.pop() << std::endl;  // World

    return 0;
}
```

### Class Template with Multiple Parameters

```cpp
template<typename Key, typename Value>
class Pair {
private:
    Key key;
    Value value;

public:
    Pair(const Key& k, const Value& v) : key(k), value(v) {}

    Key getKey() const { return key; }
    Value getValue() const { return value; }

    void setKey(const Key& k) { key = k; }
    void setValue(const Value& v) { value = v; }
};

int main() {
    Pair<std::string, int> nameAge("Alice", 25);
    Pair<int, double> idSalary(12345, 75000.50);

    std::cout << nameAge.getKey() << " is " << nameAge.getValue() << " years old" << std::endl;
    std::cout << "Employee " << idSalary.getKey() << " earns $" << idSalary.getValue() << std::endl;

    return 0;
}
```

### Member Function Templates

```cpp
template<typename T>
class Container {
private:
    std::vector<T> data;

public:
    void add(const T& item) {
        data.push_back(item);
    }

    // Member function template
    template<typename Predicate>
    std::vector<T> filter(Predicate pred) {
        std::vector<T> result;
        for (const auto& item : data) {
            if (pred(item)) {
                result.push_back(item);
            }
        }
        return result;
    }

    void print() const {
        for (const auto& item : data) {
            std::cout << item << " ";
        }
        std::cout << std::endl;
    }
};

int main() {
    Container<int> numbers;
    numbers.add(1);
    numbers.add(2);
    numbers.add(3);
    numbers.add(4);
    numbers.add(5);

    // Filter even numbers
    auto evens = numbers.filter([](int x) { return x % 2 == 0; });

    std::cout << "All numbers: ";
    numbers.print();  // 1 2 3 4 5

    std::cout << "Even numbers: ";
    for (int n : evens) {
        std::cout << n << " ";
    }
    std::cout << std::endl;  // 2 4

    return 0;
}
```

## Template Parameters

### 1. Type Parameters

```cpp
template<typename T>  // or template<class T>
void func(T value) {
    std::cout << value << std::endl;
}
```

### 2. Non-Type Parameters

```cpp
template<typename T, int SIZE>
class FixedArray {
private:
    T data[SIZE];

public:
    T& operator[](int index) {
        return data[index];
    }

    const T& operator[](int index) const {
        return data[index];
    }

    constexpr int size() const {
        return SIZE;
    }
};

int main() {
    FixedArray<int, 5> arr;
    arr[0] = 10;
    arr[1] = 20;

    std::cout << "Array size: " << arr.size() << std::endl;  // 5
    std::cout << "First element: " << arr[0] << std::endl;   // 10

    return 0;
}
```

### 3. Template Template Parameters

```cpp
template<template<typename> class Container, typename T>
class Adapter {
private:
    Container<T> container;

public:
    void add(const T& item) {
        container.push_back(item);
    }

    void print() {
        for (const auto& item : container) {
            std::cout << item << " ";
        }
        std::cout << std::endl;
    }
};

int main() {
    Adapter<std::vector, int> vectorAdapter;
    vectorAdapter.add(1);
    vectorAdapter.add(2);
    vectorAdapter.add(3);
    vectorAdapter.print();  // 1 2 3

    return 0;
}
```

### 4. Default Template Parameters

```cpp
template<typename T = int, int SIZE = 10>
class DefaultArray {
private:
    T data[SIZE];

public:
    T& operator[](int index) { return data[index]; }
    constexpr int size() const { return SIZE; }
};

int main() {
    DefaultArray<> arr1;           // int, size 10
    DefaultArray<double> arr2;     // double, size 10
    DefaultArray<char, 5> arr3;    // char, size 5

    std::cout << "arr1 size: " << arr1.size() << std::endl;  // 10
    std::cout << "arr2 size: " << arr2.size() << std::endl;  // 10
    std::cout << "arr3 size: " << arr3.size() << std::endl;  // 5

    return 0;
}
```

## Template Specialization

### Full Specialization

```cpp
// Primary template
template<typename T>
class Printer {
public:
    void print(const T& value) {
        std::cout << "Generic: " << value << std::endl;
    }
};

// Full specialization for std::string
template<>
class Printer<std::string> {
public:
    void print(const std::string& value) {
        std::cout << "String: \"" << value << "\"" << std::endl;
    }
};

// Full specialization for bool
template<>
class Printer<bool> {
public:
    void print(const bool& value) {
        std::cout << "Boolean: " << (value ? "true" : "false") << std::endl;
    }
};

int main() {
    Printer<int> intPrinter;
    Printer<std::string> stringPrinter;
    Printer<bool> boolPrinter;

    intPrinter.print(42);           // Generic: 42
    stringPrinter.print("Hello");   // String: "Hello"
    boolPrinter.print(true);        // Boolean: true

    return 0;
}
```

### Partial Specialization (Class Templates Only)

```cpp
// Primary template
template<typename T, typename U>
class Pair {
public:
    void print() {
        std::cout << "Generic pair" << std::endl;
    }
};

// Partial specialization: both types are the same
template<typename T>
class Pair<T, T> {
public:
    void print() {
        std::cout << "Same type pair" << std::endl;
    }
};

// Partial specialization: second type is int
template<typename T>
class Pair<T, int> {
public:
    void print() {
        std::cout << "Second type is int" << std::endl;
    }
};

// Partial specialization: both types are pointers
template<typename T, typename U>
class Pair<T*, U*> {
public:
    void print() {
        std::cout << "Both are pointers" << std::endl;
    }
};

int main() {
    Pair<double, char> p1;     // Generic pair
    Pair<int, int> p2;         // Same type pair
    Pair<double, int> p3;      // Second type is int
    Pair<int*, char*> p4;      // Both are pointers

    p1.print();
    p2.print();
    p3.print();
    p4.print();

    return 0;
}
```

### Function Template Specialization

```cpp
// Primary template
template<typename T>
void process(T value) {
    std::cout << "Processing generic type: " << value << std::endl;
}

// Specialization for const char*
template<>
void process<const char*>(const char* value) {
    std::cout << "Processing C-string: " << value << std::endl;
}

// Specialization for std::string
template<>
void process<std::string>(std::string value) {
    std::cout << "Processing std::string: " << value << std::endl;
}

int main() {
    process(42);                    // Processing generic type: 42
    process("Hello");               // Processing C-string: Hello
    process(std::string("World"));  // Processing std::string: World

    return 0;
}
```

## Variadic Templates

### Basic Variadic Templates

```cpp
// C++17 fold expression
template<typename... Args>
void print(Args... args) {
    ((std::cout << args << " "), ...);
    std::cout << std::endl;
}

// Pre-C++17 recursive approach
template<typename T>
void print_recursive(T&& t) {
    std::cout << t << std::endl;
}

template<typename T, typename... Args>
void print_recursive(T&& t, Args&&... args) {
    std::cout << t << " ";
    print_recursive(args...);
}

int main() {
    print(1, 2.5, "hello", 'c');           // 1 2.5 hello c
    print_recursive("Numbers:", 1, 2, 3);   // Numbers: 1 2 3

    return 0;
}
```

### Variadic Template Class

```cpp
template<typename... Types>
class Tuple;

// Specialization for empty tuple
template<>
class Tuple<> {
public:
    static constexpr size_t size() { return 0; }
};

// Recursive definition
template<typename Head, typename... Tail>
class Tuple<Head, Tail...> : private Tuple<Tail...> {
private:
    Head head;

public:
    Tuple() = default;
    Tuple(Head h, Tail... t) : Tuple<Tail...>(t...), head(h) {}

    Head& getHead() { return head; }
    const Head& getHead() const { return head; }

    Tuple<Tail...>& getTail() {
        return static_cast<Tuple<Tail...>&>(*this);
    }

    static constexpr size_t size() {
        return 1 + Tuple<Tail...>::size();
    }
};

int main() {
    Tuple<int, double, std::string> t(42, 3.14, "hello");

    std::cout << "Head: " << t.getHead() << std::endl;  // 42
    std::cout << "Size: " << t.size() << std::endl;     // 3

    return 0;
}
```

### Perfect Forwarding with Variadic Templates

```cpp
template<typename Func, typename... Args>
auto call_function(Func&& func, Args&&... args) -> decltype(func(std::forward<Args>(args)...)) {
    std::cout << "Calling function with " << sizeof...(args) << " arguments" << std::endl;
    return func(std::forward<Args>(args)...);
}

int add(int a, int b) {
    return a + b;
}

void greet(const std::string& name, int age) {
    std::cout << "Hello " << name << ", age " << age << std::endl;
}

int main() {
    auto result = call_function(add, 5, 3);
    std::cout << "Result: " << result << std::endl;

    call_function(greet, std::string("Alice"), 25);

    // With lambda
    call_function([](int x, int y, int z) {
        return x * y * z;
    }, 2, 3, 4);

    return 0;
}
```

## SFINAE and Type Traits

### SFINAE (Substitution Failure Is Not An Error)

```cpp
#include <type_traits>

// Enable if T is an integral type
template<typename T>
typename std::enable_if<std::is_integral<T>::value, T>::type
safe_divide(T a, T b) {
    if (b == 0) {
        throw std::runtime_error("Division by zero");
    }
    return a / b;
}

// Enable if T is a floating point type
template<typename T>
typename std::enable_if<std::is_floating_point<T>::value, T>::type
safe_divide(T a, T b) {
    if (std::abs(b) < std::numeric_limits<T>::epsilon()) {
        throw std::runtime_error("Division by very small number");
    }
    return a / b;
}

int main() {
    std::cout << safe_divide(10, 2) << std::endl;      // Integer division
    std::cout << safe_divide(10.0, 3.0) << std::endl;  // Float division

    // This would cause compilation error:
    // safe_divide(std::string("hello"), std::string("world"));

    return 0;
}
```

### Type Traits Detection

```cpp
// Detect if a type has a size() method
template<typename T>
auto has_size_method(T* t) -> decltype(t->size(), std::true_type{});

std::false_type has_size_method(...);

template<typename T>
constexpr bool has_size_v = decltype(has_size_method(std::declval<T*>()))::value;

// Use the trait
template<typename T>
void print_size_if_available(const T& container) {
    if constexpr (has_size_v<T>) {
        std::cout << "Size: " << container.size() << std::endl;
    } else {
        std::cout << "Size method not available" << std::endl;
    }
}

int main() {
    std::vector<int> vec = {1, 2, 3};
    int arr[] = {1, 2, 3};

    print_size_if_available(vec);  // Size: 3
    print_size_if_available(arr);  // Size method not available

    return 0;
}
```

## Template Metaprogramming

### Compile-Time Calculations

```cpp
// Factorial at compile time
template<int N>
struct Factorial {
    static constexpr int value = N * Factorial<N-1>::value;
};

template<>
struct Factorial<0> {
    static constexpr int value = 1;
};

// Fibonacci at compile time
template<int N>
struct Fibonacci {
    static constexpr int value = Fibonacci<N-1>::value + Fibonacci<N-2>::value;
};

template<>
struct Fibonacci<0> {
    static constexpr int value = 0;
};

template<>
struct Fibonacci<1> {
    static constexpr int value = 1;
};

int main() {
    constexpr int fact5 = Factorial<5>::value;  // Computed at compile time
    constexpr int fib10 = Fibonacci<10>::value;  // Computed at compile time

    std::cout << "5! = " << fact5 << std::endl;      // 120
    std::cout << "Fib(10) = " << fib10 << std::endl; // 55

    return 0;
}
```

### Type List Manipulation

```cpp
template<typename... Types>
struct TypeList {};

// Get length of type list
template<typename List>
struct Length;

template<typename... Types>
struct Length<TypeList<Types...>> {
    static constexpr size_t value = sizeof...(Types);
};

// Check if type exists in list
template<typename T, typename List>
struct Contains;

template<typename T>
struct Contains<T, TypeList<>> {
    static constexpr bool value = false;
};

template<typename T, typename Head, typename... Tail>
struct Contains<T, TypeList<Head, Tail...>> {
    static constexpr bool value = std::is_same_v<T, Head> || Contains<T, TypeList<Tail...>>::value;
};

// Get type at index
template<size_t Index, typename List>
struct At;

template<size_t Index, typename Head, typename... Tail>
struct At<Index, TypeList<Head, Tail...>> {
    using type = typename At<Index-1, TypeList<Tail...>>::type;
};

template<typename Head, typename... Tail>
struct At<0, TypeList<Head, Tail...>> {
    using type = Head;
};

int main() {
    using MyTypes = TypeList<int, double, std::string, char>;

    constexpr size_t length = Length<MyTypes>::value;  // 4
    constexpr bool hasInt = Contains<int, MyTypes>::value;  // true
    constexpr bool hasFloat = Contains<float, MyTypes>::value;  // false

    using SecondType = At<1, MyTypes>::type;  // double

    std::cout << "Length: " << length << std::endl;
    std::cout << "Has int: " << hasInt << std::endl;
    std::cout << "Has float: " << hasFloat << std::endl;

    return 0;
}
```

## Modern C++ Features

### C++14 Variable Templates

```cpp
template<typename T>
constexpr bool is_pointer_v = std::is_pointer<T>::value;

template<typename T>
constexpr bool is_integral_v = std::is_integral<T>::value;

int main() {
    static_assert(is_pointer_v<int*>);   // true
    static_assert(!is_pointer_v<int>);   // false
    static_assert(is_integral_v<int>);   // true
    static_assert(!is_integral_v<float>); // false

    return 0;
}
```

### C++17 if constexpr

```cpp
template<typename T>
void process_value(T value) {
    if constexpr (std::is_integral_v<T>) {
        std::cout << "Processing integer: " << value << std::endl;
        if constexpr (sizeof(T) > 4) {
            std::cout << "Large integer detected" << std::endl;
        }
    } else if constexpr (std::is_floating_point_v<T>) {
        std::cout << "Processing float: " << value << std::endl;
    } else if constexpr (std::is_same_v<T, std::string>) {
        std::cout << "Processing string: " << value << std::endl;
    } else {
        std::cout << "Processing unknown type" << std::endl;
    }
}

int main() {
    process_value(42);              // Processing integer: 42
    process_value(3.14);            // Processing float: 3.14
    process_value(std::string("hi")); // Processing string: hi

    long long big = 123456789LL;
    process_value(big);             // Processing integer: 123456789
                                   // Large integer detected

    return 0;
}
```

### C++20 Concepts

```cpp
#include <concepts>

// Define concepts
template<typename T>
concept Numeric = std::is_arithmetic_v<T>;

template<typename T>
concept Container = requires(T t) {
    t.begin();
    t.end();
    t.size();
};

template<typename T>
concept Printable = requires(T t, std::ostream& os) {
    os << t;
};

// Use concepts in templates
template<Numeric T>
T square(T value) {
    return value * value;
}

template<Container C>
void print_container(const C& container) {
    std::cout << "Container contents: ";
    for (const auto& element : container) {
        std::cout << element << " ";
    }
    std::cout << std::endl;
}

template<Printable T>
void debug_print(const T& value) {
    std::cout << "Debug: " << value << std::endl;
}

// Concept with requires clause
template<typename T>
requires Numeric<T> && sizeof(T) >= 4
T large_square(T value) {
    return value * value;
}

int main() {
    std::cout << square(5) << std::endl;        // 25
    std::cout << square(3.14) << std::endl;     // 9.8596

    std::vector<int> vec = {1, 2, 3, 4, 5};
    print_container(vec);

    debug_print(42);
    debug_print("Hello World");

    std::cout << large_square(10) << std::endl;     // Works with int
    std::cout << large_square(5.5) << std::endl;    // Works with double
    // large_square(char('a'));  // Error: sizeof(char) < 4

    return 0;
}
```

## Advanced Topics

### CRTP (Curiously Recurring Template Pattern)

```cpp
template<typename Derived>
class Shape {
public:
    void draw() {
        static_cast<Derived*>(this)->draw_impl();
    }

    double area() {
        return static_cast<Derived*>(this)->area_impl();
    }

    void print_info() {
        std::cout << "Shape area: " << area() << std::endl;
        draw();
    }
};

class Circle : public Shape<Circle> {
private:
    double radius;

public:
    Circle(double r) : radius(r) {}

    void draw_impl() {
        std::cout << "Drawing a circle" << std::endl;
    }

    double area_impl() {
        return 3.14159 * radius * radius;
    }
};

class Rectangle : public Shape<Rectangle> {
private:
    double width, height;

public:
    Rectangle(double w, double h) : width(w), height(h) {}

    void draw_impl() {
        std::cout << "Drawing a rectangle" << std::endl;
    }

    double area_impl() {
        return width * height;
    }
};

int main() {
    Circle circle(5.0);
    Rectangle rect(4.0, 6.0);

    circle.print_info();    // Shape area: 78.5397, Drawing a circle
    rect.print_info();      // Shape area: 24, Drawing a rectangle

    return 0;
}
```

### Expression Templates

```cpp
template<typename E>
class VecExpression {
public:
    double operator[](size_t i) const {
        return static_cast<const E&>(*this)[i];
    }

    size_t size() const {
        return static_cast<const E&>(*this).size();
    }
};

class Vec : public VecExpression<Vec> {
private:
    std::vector<double> data;

public:
    Vec(std::initializer_list<double> init) : data(init) {}

    double operator[](size_t i) const { return data[i]; }
    double& operator[](size_t i) { return data[i]; }
    size_t size() const { return data.size(); }

    template<typename E>
    Vec& operator=(const VecExpression<E>& expr) {
        for (size_t i = 0; i < size(); ++i) {
            data[i] = expr[i];
        }
        return *this;
    }
};

template<typename E1, typename E2>
class VecAdd : public VecExpression<VecAdd<E1, E2>> {
private:
    const E1& u;
    const E2& v;

public:
    VecAdd(const E1& u, const E2& v) : u(u), v(v) {}

    double operator[](size_t i) const {
        return u[i] + v[i];
    }

    size_t size() const {
        return u.size();
    }
};

template<typename E1, typename E2>
VecAdd<E1, E2> operator+(const VecExpression<E1>& u, const VecExpression<E2>& v) {
    return VecAdd<E1, E2>(static_cast<const E1&>(u), static_cast<const E2&>(v));
}

int main() {
    Vec a = {1.0, 2.0, 3.0};
    Vec b = {4.0, 5.0, 6.0};
    Vec c = {7.0, 8.0, 9.0};

    Vec result = {0.0, 0.0, 0.0};
    result = a + b + c;  // No temporary vectors created!

    for (size_t i = 0; i < result.size(); ++i) {
        std::cout << result[i] << " ";  // 12 15 18
    }
    std::cout << std::endl;

    return 0;
}
```

## Best Practices

### 1. Use Clear Template Parameter Names

```cpp
// Good: Clear and descriptive
template<typename ValueType, typename AllocatorType = std::allocator<ValueType>>
class MyContainer { /* ... */ };

// Bad: Unclear abbreviations
template<typename T, typename A = std::allocator<T>>
class MyContainer { /* ... */ };
```

### 2. Provide Clear Error Messages

```cpp
template<typename T>
class NumericProcessor {
    static_assert(std::is_arithmetic_v<T>,
                  "NumericProcessor requires arithmetic types (int, float, double, etc.)");
public:
    T process(T value) {
        return value * 2;
    }
};

// With concepts (C++20) - even better
template<typename T>
requires std::is_arithmetic_v<T>
class NumericProcessorV2 {
public:
    T process(T value) {
        return value * 2;
    }
};
```

### 3. Use Template Argument Deduction When Possible

```cpp
// Good: Let compiler deduce types
template<typename T>
auto make_unique_ptr(T value) {
    return std::make_unique<T>(std::move(value));
}

// Usage
auto ptr = make_unique_ptr(42);  // Type deduced as std::unique_ptr<int>
```

### 4. Prefer `typename` over `class` for Type Parameters

```cpp
// Preferred
template<typename T>
class MyClass { /* ... */ };

// Less preferred (though functionally identical)
template<class T>
class MyClass { /* ... */ };
```

### 5. Use Perfect Forwarding for Generic Functions

```cpp
template<typename T>
void wrapper(T&& arg) {
    some_function(std::forward<T>(arg));  // Preserves value category
}
```

### 6. Separate Template Declarations and Definitions Carefully

```cpp
// In header file (.h)
template<typename T>
class MyClass {
public:
    void method();
    T process(const T& value);
};

// Template definitions must be in header or included in every translation unit
template<typename T>
void MyClass<T>::method() {
    // Implementation
}

template<typename T>
T MyClass<T>::process(const T& value) {
    return value;
}
```

## Common Pitfalls

### 1. Template Instantiation Bloat

```cpp
// BAD: Each instantiation creates separate code
template<typename T>
void expensive_function(T value) {
    // 1000 lines of code that doesn't depend on T
    for (int i = 0; i < 1000000; ++i) {
        std::cout << "Processing..." << std::endl;
    }

    // Only this line actually needs to be templated
    std::cout << "Value: " << value << std::endl;
}

// GOOD: Extract non-templated parts
void expensive_processing() {
    for (int i = 0; i < 1000000; ++i) {
        std::cout << "Processing..." << std::endl;
    }
}

template<typename T>
void efficient_function(T value) {
    expensive_processing();  // Non-templated
    std::cout << "Value: " << value << std::endl;  // Templated part
}
```

### 2. Dangling References in Template Returns

```cpp
// BAD: Returning reference to local variable
template<typename T>
const T& bad_function(T a, T b) {
    T result = a + b;  // Local variable
    return result;     // Dangling reference!
}

// GOOD: Return by value for local variables
template<typename T>
T good_function(T a, T b) {
    return a + b;  // Return by value
}
```

### 3. Forgetting Template Keyword in Dependent Names

```cpp
template<typename T>
class Container {
public:
    template<typename U>
    void insert(U value) { /* ... */ }
};

template<typename T>
void process_container(Container<T>& container) {
    // BAD: Compiler doesn't know insert is a template
    // container.insert<int>(42);  // Error!

    // GOOD: Use template keyword for dependent names
    container.template insert<int>(42);
}
```

### 4. Name Lookup Issues

```cpp
void helper() {
    std::cout << "Global helper" << std::endl;
}

template<typename T>
void template_function() {
    helper();  // Always calls global helper (phase 1 lookup)
}

namespace ns {
    void helper() {
        std::cout << "Namespace helper" << std::endl;
    }

    class MyClass {};
}

template<>
void template_function<ns::MyClass>() {
    helper();  // Still calls global helper, not ns::helper!
}
```

### 5. Incomplete Type Issues

```cpp
// Forward declaration
class MyClass;

// BAD: Using incomplete type in template
template<typename T>
class Container {
    T value;  // Error if T is incomplete
public:
    Container() : value() {}  // Error: incomplete type
};

// Container<MyClass> c;  // Error: MyClass is incomplete

// Define MyClass first
class MyClass {
public:
    MyClass() = default;
};

// Now it works
Container<MyClass> c;  // OK
```

## Real-World Applications

### 1. Smart Pointer Implementation

```cpp
template<typename T>
class unique_ptr {
private:
    T* ptr;

public:
    // Constructor
    explicit unique_ptr(T* p = nullptr) : ptr(p) {}

    // Destructor
    ~unique_ptr() {
        delete ptr;
    }

    // Move constructor
    unique_ptr(unique_ptr&& other) noexcept : ptr(other.ptr) {
        other.ptr = nullptr;
    }

    // Move assignment
    unique_ptr& operator=(unique_ptr&& other) noexcept {
        if (this != &other) {
            delete ptr;
            ptr = other.ptr;
            other.ptr = nullptr;
        }
        return *this;
    }

    // Delete copy operations
    unique_ptr(const unique_ptr&) = delete;
    unique_ptr& operator=(const unique_ptr&) = delete;

    // Operators
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }

    // Utility functions
    T* get() const { return ptr; }
    T* release() {
        T* temp = ptr;
        ptr = nullptr;
        return temp;
    }

    void reset(T* p = nullptr) {
        delete ptr;
        ptr = p;
    }

    explicit operator bool() const {
        return ptr != nullptr;
    }
};

// Factory function
template<typename T, typename... Args>
unique_ptr<T> make_unique(Args&&... args) {
    return unique_ptr<T>(new T(std::forward<Args>(args)...));
}

int main() {
    auto ptr = make_unique<std::string>("Hello World");
    std::cout << *ptr << std::endl;  // Hello World

    auto ptr2 = std::move(ptr);      // Transfer ownership
    // ptr is now nullptr, ptr2 owns the string

    if (ptr2) {
        std::cout << "ptr2 is valid: " << *ptr2 << std::endl;
    }

    return 0;
}  // Automatic cleanup when ptr2 goes out of scope
```

### 2. Type-Safe Event System

```cpp
template<typename... Args>
class Event {
private:
    using HandlerType = std::function<void(Args...)>;
    std::vector<HandlerType> handlers;

public:
    void subscribe(HandlerType handler) {
        handlers.push_back(handler);
    }

    void emit(Args... args) {
        for (auto& handler : handlers) {
            handler(args...);
        }
    }

    void clear() {
        handlers.clear();
    }
};

// Usage example
class GameEngine {
private:
    Event<int, int> onPlayerMove;           // x, y coordinates
    Event<std::string> onPlayerDeath;       // death reason
    Event<> onGameStart;                    // no parameters

public:
    void setupEvents() {
        onPlayerMove.subscribe([](int x, int y) {
            std::cout << "Player moved to (" << x << ", " << y << ")" << std::endl;
        });

        onPlayerDeath.subscribe([](const std::string& reason) {
            std::cout << "Player died: " << reason << std::endl;
        });

        onGameStart.subscribe([]() {
            std::cout << "Game started!" << std::endl;
        });
    }

    void movePlayer(int x, int y) {
        // Game logic...
        onPlayerMove.emit(x, y);
    }

    void killPlayer(const std::string& reason) {
        // Game logic...
        onPlayerDeath.emit(reason);
    }

    void startGame() {
        // Game logic...
        onGameStart.emit();
    }
};

int main() {
    GameEngine game;
    game.setupEvents();

    game.startGame();                    // Game started!
    game.movePlayer(10, 20);             // Player moved to (10, 20)
    game.killPlayer("Fell into lava");   // Player died: Fell into lava

    return 0;
}
```

### 3. Compile-Time String Processing

```cpp
template<size_t N>
struct CompileTimeString {
    char data[N];

    constexpr CompileTimeString(const char (&str)[N]) {
        for (size_t i = 0; i < N; ++i) {
            data[i] = str[i];
        }
    }

    constexpr size_t size() const { return N - 1; }  // Exclude null terminator
    constexpr const char* c_str() const { return data; }
};

template<CompileTimeString str>
constexpr auto reverse_string() {
    constexpr size_t len = str.size();
    char reversed[len + 1] = {};

    for (size_t i = 0; i < len; ++i) {
        reversed[i] = str.data[len - 1 - i];
    }

    return CompileTimeString<len + 1>(reversed);
}

template<CompileTimeString str>
constexpr bool is_palindrome() {
    constexpr size_t len = str.size();
    for (size_t i = 0; i < len / 2; ++i) {
        if (str.data[i] != str.data[len - 1 - i]) {
            return false;
        }
    }
    return true;
}

int main() {
    constexpr auto reversed = reverse_string<"hello">();
    constexpr bool is_pal1 = is_palindrome<"racecar">();
    constexpr bool is_pal2 = is_palindrome<"hello">();

    std::cout << "Reversed 'hello': " << reversed.c_str() << std::endl;  // olleh
    std::cout << "'racecar' is palindrome: " << is_pal1 << std::endl;    // 1 (true)
    std::cout << "'hello' is palindrome: " << is_pal2 << std::endl;      // 0 (false)

    return 0;
}
```

### 4. Generic Data Structure: Binary Tree

```cpp
template<typename T, typename Compare = std::less<T>>
class BinarySearchTree {
private:
    struct Node {
        T data;
        std::unique_ptr<Node> left;
        std::unique_ptr<Node> right;

        Node(const T& value) : data(value) {}
    };

    std::unique_ptr<Node> root;
    Compare comp;

    void insert_helper(std::unique_ptr<Node>& node, const T& value) {
        if (!node) {
            node = std::make_unique<Node>(value);
            return;
        }

        if (comp(value, node->data)) {
            insert_helper(node->left, value);
        } else if (comp(node->data, value)) {
            insert_helper(node->right, value);
        }
        // If equal, don't insert (no duplicates)
    }

    bool search_helper(const std::unique_ptr<Node>& node, const T& value) const {
        if (!node) return false;

        if (comp(value, node->data)) {
            return search_helper(node->left, value);
        } else if (comp(node->data, value)) {
            return search_helper(node->right, value);
        } else {
            return true;  // Found
        }
    }

    void inorder_helper(const std::unique_ptr<Node>& node, std::vector<T>& result) const {
        if (node) {
            inorder_helper(node->left, result);
            result.push_back(node->data);
            inorder_helper(node->right, result);
        }
    }

public:
    BinarySearchTree() = default;

    void insert(const T& value) {
        insert_helper(root, value);
    }

    bool search(const T& value) const {
        return search_helper(root, value);
    }

    std::vector<T> inorder() const {
        std::vector<T> result;
        inorder_helper(root, result);
        return result;
    }

    bool empty() const {
        return !root;
    }
};

int main() {
    // Tree with integers
    BinarySearchTree<int> intTree;
    intTree.insert(5);
    intTree.insert(3);
    intTree.insert(7);
    intTree.insert(1);
    intTree.insert(9);

    auto values = intTree.inorder();
    std::cout << "Inorder traversal: ";
    for (int val : values) {
        std::cout << val << " ";  // 1 3 5 7 9
    }
    std::cout << std::endl;

    // Tree with custom comparison (descending order)
    BinarySearchTree<int, std::greater<int>> descTree;
    descTree.insert(5);
    descTree.insert(3);
    descTree.insert(7);

    auto descValues = descTree.inorder();
    std::cout << "Descending order: ";
    for (int val : descValues) {
        std::cout << val << " ";  // 7 5 3
    }
    std::cout << std::endl;

    // Tree with strings
    BinarySearchTree<std::string> stringTree;
    stringTree.insert("apple");
    stringTree.insert("banana");
    stringTree.insert("cherry");

    std::cout << "Has 'banana': " << stringTree.search("banana") << std::endl;  // 1
    std::cout << "Has 'grape': " << stringTree.search("grape") << std::endl;    // 0

    return 0;
}
```

## Conclusion

Templates are one of C++'s most powerful features, enabling:

1. **Generic Programming** - Write code once, use with many types
2. **Type Safety** - Catch errors at compile time
3. **Performance** - Zero runtime overhead
4. **Metaprogramming** - Compute at compile time
5. **Modern C++** - Foundation of STL and modern libraries

### Key Takeaways:

- **Start Simple**: Begin with basic function and class templates
- **Understand Instantiation**: Templates generate code when used
- **Use Specialization**: Customize behavior for specific types
- **Embrace Modern Features**: Concepts, `if constexpr`, auto deduction
- **Practice SFINAE**: Enable/disable templates based on type properties
- **Learn Metaprogramming**: Compute types and values at compile time

### Learning Path:

1. **Beginner**: Function templates, class templates, basic specialization
2. **Intermediate**: Variadic templates, SFINAE, perfect forwarding
3. **Advanced**: Metaprogramming, expression templates, concepts
4. **Expert**: Library design, complex template patterns

### Next Steps:

1. Practice with STL source code reading
2. Implement your own containers and algorithms
3. Study modern template libraries (ranges, concepts)
4. Build template-heavy projects

Remember: Templates have a learning curve, but they're essential for modern C++. Start with simple examples and gradually build complexity. The investment in learning templates pays off enormously in code quality and performance!
