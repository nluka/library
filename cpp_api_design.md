# C++ API Design

## \[\[nodiscard]]

- instructs the compiler to issue a warning if a return value is dropped

- can be applied to functions, lambdas, types
```cpp
// function
[[nodiscard]] int get_val();

// lambda (C++23)
auto l = [] [[nodiscard]] () -> int { return 42; }; // applied to the call operator

// type
struct [[nodiscard]] error_type{};
error_type get_val();
```

- can have an explanation message
```cpp
[[nodiscard("Explanation message")]]
```

- although the standard does not require the compiler to give a warning, any reasonable compiler will

- sadly an enum class can't be marked nodiscard (maybe this will change in a future standard)

## never return a raw pointer

- simply raises too many questions - who owns it? who deletes it? is it a singleton global?

- consider `owning_ptr`, `non_owning_ptr`, or some kind of wrapper to document intent if you must return a raw pointer

## provide consistent, impossible to ignore error handling with in-band reporting of what went wrong

- use one consistent method of reporting errors in your library

- strongly avoid out-of-band error reporting (`get_last_error()` or `errno`) because
  - a) nobody will use it
  - b) it may destroy your ability to make the library multi-thread friendly

- make errors impossible to ignore (not returning an error code!)

- you don't want the possibility of your application getting into an unknown state

- never use `std::optional` to indicate an error condition, it does not convey a reason, and the reason becomes out-of-band

- consider `std::expected` or similar, `std::error_code`

## avoid easily swappable parameters

- two or more parameters beside each other of the same type are easy to swap and bugprone
