# C++ Lambda Functions: From Zero to Hero

## Table of Contents

1. [What are Lambda Functions?](#what-are-lambda-functions)
2. [Basic Syntax](#basic-syntax)
3. [Capture Clauses](#capture-clauses)
4. [Parameter Lists](#parameter-lists)
5. [Return Types](#return-types)
6. [Practical Examples](#practical-examples)
7. [Advanced Topics](#advanced-topics)
8. [Best Practices](#best-practices)
9. [Common Pitfalls](#common-pitfalls)
10. [Real-World Applications](#real-world-applications)

## What are Lambda Functions?

Lambda functions (also called lambda expressions) are anonymous functions introduced in C++11. They allow you to define functions inline without giving them a name, making your code more concise and readable.

**Why use lambdas?**

- Write shorter, more readable code
- Define functions close to where they're used
- Perfect for callbacks and functional programming
- Great with STL algorithms

## Basic Syntax

The general syntax of a lambda function is:

```cpp
[capture](parameters) -> return_type { body }
```

Let's break this down:

- `[capture]`: Capture clause - defines what variables from the surrounding scope can be accessed
- `(parameters)`: Parameter list (optional if no parameters)
- `-> return_type`: Return type (optional, can be deduced)
- `{ body }`: Function body

### Your First Lambda

```cpp
#include <iostream>

int main() {
    // Simple lambda that prints "Hello, Lambda!"
    auto hello = []() {
        std::cout << "Hello, Lambda!" << std::endl;
    };

    hello(); // Call the lambda

    // Even simpler - lambda without parameters
    auto greet = [] {
        std::cout << "Hi there!" << std::endl;
    };

    greet();

    return 0;
}
```

## Capture Clauses

The capture clause `[]` is what makes lambdas special. It defines how variables from the surrounding scope are captured.

### Types of Capture

#### 1. No Capture `[]`

```cpp
int main() {
    int x = 10;

    auto lambda = []() {
        // std::cout << x; // ERROR! Cannot access x
        std::cout << "No access to outer variables" << std::endl;
    };

    lambda();
    return 0;
}
```

#### 2. Capture by Value `[=]`

```cpp
int main() {
    int x = 10;
    int y = 20;

    auto lambda = [=]() {
        std::cout << "x: " << x << ", y: " << y << std::endl;
        // x = 15; // ERROR! Cannot modify captured values
    };

    lambda(); // Output: x: 10, y: 20
    return 0;
}
```

#### 3. Capture by Reference `[&]`

```cpp
int main() {
    int x = 10;
    int y = 20;

    auto lambda = [&]() {
        x = 15; // Can modify x
        y = 25; // Can modify y
        std::cout << "x: " << x << ", y: " << y << std::endl;
    };

    lambda(); // Output: x: 15, y: 25
    std::cout << "After lambda - x: " << x << ", y: " << y << std::endl; // x: 15, y: 25
    return 0;
}
```

#### 4. Specific Captures

```cpp
int main() {
    int x = 10;
    int y = 20;
    int z = 30;

    // Capture x by value, y by reference
    auto lambda = [x, &y]() {
        std::cout << "x: " << x << std::endl;     // x copied
        y = 100;                                   // y modified by reference
        // std::cout << z; // ERROR! z not captured
    };

    lambda();
    std::cout << "y after lambda: " << y << std::endl; // y: 100
    return 0;
}
```

#### 5. Mixed Captures

```cpp
int main() {
    int a = 1, b = 2, c = 3, d = 4;

    // Capture all by value, but b by reference
    auto lambda1 = [=, &b]() {
        std::cout << "a: " << a << ", b: " << b << ", c: " << c << ", d: " << d << std::endl;
        b = 200; // Only b can be modified
    };

    // Capture all by reference, but c by value
    auto lambda2 = [&, c]() {
        a = 10; b = 20; d = 40; // a, b, d modified by reference
        // c = 30; // ERROR! c is captured by value
        std::cout << "c (copy): " << c << std::endl;
    };

    lambda1();
    lambda2();

    return 0;
}
```

## Parameter Lists

Lambdas can accept parameters just like regular functions:

```cpp
#include <iostream>
#include <string>

int main() {
    // Lambda with parameters
    auto add = [](int a, int b) {
        return a + b;
    };

    std::cout << "5 + 3 = " << add(5, 3) << std::endl;

    // Lambda with different parameter types
    auto greet = [](const std::string& name, int age) {
        std::cout << "Hello " << name << ", you are " << age << " years old!" << std::endl;
    };

    greet("Alice", 25);

    // Lambda with auto parameters (C++14)
    auto multiply = [](auto a, auto b) {
        return a * b;
    };

    std::cout << "3.5 * 2 = " << multiply(3.5, 2) << std::endl;
    std::cout << "4 * 5 = " << multiply(4, 5) << std::endl;

    return 0;
}
```

## Return Types

### Automatic Return Type Deduction

```cpp
int main() {
    // Return type automatically deduced as int
    auto square = [](int x) {
        return x * x;
    };

    std::cout << "Square of 5: " << square(5) << std::endl;

    return 0;
}
```

### Explicit Return Type

```cpp
int main() {
    // Explicitly specify return type
    auto divide = [](double a, double b) -> double {
        if (b != 0) {
            return a / b;
        }
        return 0.0;
    };

    std::cout << "10.0 / 3.0 = " << divide(10.0, 3.0) << std::endl;

    return 0;
}
```



### Multiple Return Statements

```cpp
int main() {
    // When you have multiple return statements with different types,
    // you need to specify the return type
    auto compare = [](int a, int b) -> std::string {
        if (a > b) {
            return "greater";
        } else if (a < b) {
            return "less";
        } else {
            return "equal";
        }
    };

    std::cout << "5 compared to 3: " << compare(5, 3) << std::endl;

    return 0;
}
```

## Practical Examples

### 1. Using Lambdas with STL Algorithms

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

    // Find even numbers
    std::cout << "Even numbers: ";
    std::for_each(numbers.begin(), numbers.end(), [](int n) {
        if (n % 2 == 0) {
            std::cout << n << " ";
        }
    });
    std::cout << std::endl;

    // Count numbers greater than 5
    int count = std::count_if(numbers.begin(), numbers.end(), [](int n) {
        return n > 5;
    });
    std::cout << "Numbers > 5: " << count << std::endl;

    // Transform: square all numbers
    std::vector<int> squares;
    std::transform(numbers.begin(), numbers.end(), std::back_inserter(squares),
                   [](int n) { return n * n; });

    std::cout << "Squares: ";
    for (int sq : squares) {
        std::cout << sq << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### 2. Custom Sorting

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

struct Person {
    std::string name;
    int age;
};

int main() {
    std::vector<Person> people = {
        {"Alice", 30},
        {"Bob", 25},
        {"Charlie", 35},
        {"Diana", 28}
    };

    // Sort by age
    std::sort(people.begin(), people.end(), [](const Person& a, const Person& b) {
        return a.age < b.age;
    });

    std::cout << "Sorted by age:" << std::endl;
    for (const auto& person : people) {
        std::cout << person.name << " (" << person.age << ")" << std::endl;
    }

    // Sort by name length
    std::sort(people.begin(), people.end(), [](const Person& a, const Person& b) {
        return a.name.length() < b.name.length();
    });

    std::cout << "\nSorted by name length:" << std::endl;
    for (const auto& person : people) {
        std::cout << person.name << " (" << person.age << ")" << std::endl;
    }

    return 0;
}
```

### 3. Event Handling / Callbacks

```cpp
#include <iostream>
#include <functional>
#include <vector>

class Button {
private:
    std::function<void()> onClick;

public:
    void setOnClick(std::function<void()> callback) {
        onClick = callback;
    }

    void click() {
        if (onClick) {
            onClick();
        }
    }
};

int main() {
    Button button;
    int clickCount = 0;

    // Set lambda as callback
    button.setOnClick([&clickCount]() {
        clickCount++;
        std::cout << "Button clicked! Count: " << clickCount << std::endl;
    });

    button.click(); // Button clicked! Count: 1
    button.click(); // Button clicked! Count: 2
    button.click(); // Button clicked! Count: 3

    return 0;
}
```

## Advanced Topics

### 1. Mutable Lambdas

```cpp
int main() {
    int x = 10;

    // Mutable lambda - can modify captured values
    auto counter = [x]() mutable {
        x++;
        std::cout << "Counter: " << x << std::endl;
        return x;
    };

    counter(); // Counter: 11
    counter(); // Counter: 12
    counter(); // Counter: 13

    std::cout << "Original x: " << x << std::endl; // Original x: 10 (unchanged)

    return 0;
}
```

### 2. Generic Lambdas (C++14)

```cpp
#include <iostream>
#include <string>
#include <vector>

int main() {
    // Generic lambda with auto parameters
    auto printer = [](const auto& container) {
        for (const auto& item : container) {
            std::cout << item << " ";
        }
        std::cout << std::endl;
    };

    std::vector<int> numbers = {1, 2, 3, 4, 5};
    std::vector<std::string> words = {"hello", "world", "lambda"};

    printer(numbers); // 1 2 3 4 5
    printer(words);   // hello world lambda

    return 0;
}
```

### 3. Lambda with Perfect Forwarding

```cpp
#include <iostream>
#include <utility>

template<typename Func, typename... Args>
auto call_function(Func&& func, Args&&... args) {
    return func(std::forward<Args>(args)...);
}

int main() {
    auto lambda = [](auto&& x, auto&& y) {
        return std::forward<decltype(x)>(x) + std::forward<decltype(y)>(y);
    };

    int a = 5;
    int b = 10;

    auto result = call_function(lambda, a, b);
    std::cout << "Result: " << result << std::endl;

    return 0;
}
```

### 4. Recursive Lambdas

```cpp
#include <iostream>
#include <functional>

int main() {
    // Recursive lambda for factorial
    std::function<int(int)> factorial = [&factorial](int n) -> int {
        return (n <= 1) ? 1 : n * factorial(n - 1);
    };

    std::cout << "5! = " << factorial(5) << std::endl; // 5! = 120

    return 0;
}
```

### 5. Immediately Invoked Lambda Expression (IILE)

```cpp
#include <iostream>

int main() {
    // Lambda that's immediately invoked
    int result = [](int x, int y) {
        return x * y + 10;
    }(5, 3); // Immediately called with arguments 5 and 3

    std::cout << "Result: " << result << std::endl; // Result: 25

    // Useful for complex initialization
    const auto config = []() {
        // Complex logic here
        if (/* some condition */ true) {
            return "development";
        } else {
            return "production";
        }
    }();

    std::cout << "Config: " << config << std::endl;

    return 0;
}
```

## Best Practices

### 1. Prefer Lambdas for Short Functions

```cpp
// Good: Short, clear lambda
std::sort(vec.begin(), vec.end(), [](int a, int b) { return a < b; });

// Bad: Lambda too complex, use regular function instead
auto complex_lambda = [](const ComplexType& a, const ComplexType& b) {
    // 20+ lines of complex logic
    // This should be a regular function
};
```

### 2. Be Careful with Captures

```cpp
std::function<int()> create_counter() {
    int count = 0;

    // Dangerous: capturing local variable by reference
    return [&count]() mutable { return ++count; }; // BAD: count is destroyed

    // Better: capture by value or use shared_ptr
    return [count]() mutable { return ++count; }; // GOOD
}
```

### 3. Use `auto` for Lambda Variables

```cpp
// Good
auto lambda = [](int x) { return x * 2; };

// Unnecessarily verbose
std::function<int(int)> lambda2 = [](int x) { return x * 2; };
```

### 4. Prefer Capture by Value for Simple Types

```cpp
int x = 10;
std::string name = "John";

// Good for simple types
auto lambda1 = [x, name]() { /* ... */ };

// Use reference only when necessary
auto lambda2 = [&x]() { x++; }; // When you need to modify
```

## Common Pitfalls

### 1. Dangling References

```cpp
std::function<void()> create_printer() {
    std::string message = "Hello";

    // BAD: message will be destroyed when function returns
    return [&message]() { std::cout << message << std::endl; };

    // GOOD: capture by value
    return [message]() { std::cout << message << std::endl; };
}
```

### 2. Capturing `this` in Member Functions

```cpp
class MyClass {
private:
    int value = 42;

public:
    auto get_lambda() {
        // Capture this pointer
        return [this]() { return value; }; // Be careful about object lifetime

        // Safer: capture by value (C++17)
        return [*this]() { return value; }; // Copies entire object

        // Or capture specific members
        return [value = this->value]() { return value; };
    }
};
```

### 3. Modifying Captured Variables

```cpp
int main() {
    int x = 10;

    // This won't modify the original x
    auto lambda1 = [x]() mutable { x++; }; // x is a copy

    // This will modify the original x
    auto lambda2 = [&x]() { x++; }; // x is a reference

    lambda1();
    std::cout << "x after lambda1: " << x << std::endl; // Still 10

    lambda2();
    std::cout << "x after lambda2: " << x << std::endl; // Now 11

    return 0;
}
```

## Real-World Applications

### 1. Parallel Processing with Lambdas

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <execution> // C++17

int main() {
    std::vector<int> large_vector(1000000);
    std::iota(large_vector.begin(), large_vector.end(), 1);

    // Parallel transform using lambda
    std::transform(std::execution::par_unseq,
                   large_vector.begin(), large_vector.end(),
                   large_vector.begin(),
                   [](int x) { return x * x; });

    std::cout << "First 10 squares: ";
    for (int i = 0; i < 10; ++i) {
        std::cout << large_vector[i] << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### 2. Functional Programming Style

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <functional>

template<typename Container, typename Predicate>
auto filter(const Container& container, Predicate pred) {
    Container result;
    std::copy_if(container.begin(), container.end(),
                 std::back_inserter(result), pred);
    return result;
}

template<typename Container, typename Transform>
auto map(const Container& container, Transform trans) {
    std::vector<decltype(trans(*container.begin()))> result;
    std::transform(container.begin(), container.end(),
                   std::back_inserter(result), trans);
    return result;
}

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

    // Functional programming pipeline
    auto evens = filter(numbers, [](int x) { return x % 2 == 0; });
    auto squares = map(evens, [](int x) { return x * x; });

    auto sum = std::accumulate(squares.begin(), squares.end(), 0,
                              [](int acc, int x) { return acc + x; });

    std::cout << "Sum of squares of even numbers: " << sum << std::endl;

    return 0;
}
```

### 3. State Machines with Lambdas

```cpp
#include <iostream>
#include <functional>
#include <map>

enum class State { IDLE, RUNNING, PAUSED, STOPPED };
enum class Event { START, PAUSE, RESUME, STOP };

class StateMachine {
private:
    State current_state = State::IDLE;
    std::map<std::pair<State, Event>, std::function<State()>> transitions;

public:
    StateMachine() {
        // Define state transitions using lambdas
        transitions[{State::IDLE, Event::START}] = [this]() {
            std::cout << "Starting..." << std::endl;
            return State::RUNNING;
        };

        transitions[{State::RUNNING, Event::PAUSE}] = [this]() {
            std::cout << "Pausing..." << std::endl;
            return State::PAUSED;
        };

        transitions[{State::PAUSED, Event::RESUME}] = [this]() {
            std::cout << "Resuming..." << std::endl;
            return State::RUNNING;
        };

        transitions[{State::RUNNING, Event::STOP}] = [this]() {
            std::cout << "Stopping..." << std::endl;
            return State::STOPPED;
        };
    }

    bool handle_event(Event event) {
        auto it = transitions.find({current_state, event});
        if (it != transitions.end()) {
            current_state = it->second();
            return true;
        }
        return false;
    }

    State get_state() const { return current_state; }
};
```

## Conclusion

Lambda functions are a powerful feature in modern C++ that can make your code more concise, readable, and expressive. Here's what we've covered:

1. **Basic syntax** and structure of lambdas
2. **Capture clauses** - how to access variables from surrounding scope
3. **Parameters and return types** - making lambdas flexible
4. **Practical applications** with STL algorithms
5. **Advanced features** like generic lambdas and recursion
6. **Best practices** and common pitfalls to avoid
7. **Real-world applications** in various programming scenarios

### Key Takeaways:

- Lambdas are perfect for short, inline functions
- Be careful with capture clauses to avoid dangling references
- Use lambdas extensively with STL algorithms
- They're great for callbacks and event handling
- Modern C++ heavily relies on lambdas for clean, functional-style code

### Next Steps:

1. Practice writing lambdas with different STL algorithms
2. Experiment with different capture modes
3. Try implementing functional programming patterns
4. Use lambdas in real projects for callbacks and event handling

Remember: The best way to master lambdas is through practice. Start with simple examples and gradually work your way up to more complex scenarios!

