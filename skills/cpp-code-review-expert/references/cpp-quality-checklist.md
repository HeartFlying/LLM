# C++ Code Quality Checklist

## Performance & Efficiency

### Memory Management Performance

- **Unnecessary allocations**: Avoid heap allocations in hot paths
  ```cpp
  // BAD: Allocation in loop
  for (int i = 0; i < 1000; ++i) {
      std::string temp = "prefix_" + std::to_string(i);
      process(temp);
  }
  
  // GOOD: Reserve capacity or reuse objects
  std::string temp;
  temp.reserve(50);  // Allocate once
  for (int i = 0; i < 1000; ++i) {
      temp = "prefix_" + std::to_string(i);
      process(temp);
  }
  ```

- **Move semantics**: Missing move constructor/assignment for expensive objects
- **Smart pointer overhead**: Using `shared_ptr` when `unique_ptr` suffices
- **Vector resizing**: Not using `reserve()` when final size is known
- **Small string optimization**: Missing out on SSO benefits

### Algorithmic Complexity

- **O(n²) in hot paths**: Nested loops where O(n log n) or O(n) alternatives exist
  ```cpp
  // BAD: O(n²) search
  for (const auto& item1 : container1) {
      for (const auto& item2 : container2) {
          if (item1.id == item2.id) {
              // Process match
          }
      }
  }
  
  // GOOD: O(n) with hash map
  std::unordered_set<int> ids;
  for (const auto& item : container2) {
      ids.insert(item.id);
  }
  for (const auto& item : container1) {
      if (ids.contains(item.id)) {
          // Process match
      }
  }
  ```

- **Inefficient data structures**: Using wrong container for use case
- **Unnecessary copies**: Passing by value instead of const reference
- **Redundant computations**: Computing same value multiple times

### Compiler Optimization

- **Branch prediction**: Poorly predictable branches in hot code
- **Cache locality**: Poor memory access patterns causing cache misses
- **Inline assembly**: Unnecessary or poorly optimized inline assembly
- **Template instantiation**: Excessive template instantiation leading to code bloat

## Error Handling & Robustness

### Exception Safety

- **Basic exception safety**: Resource leaks when exceptions occur
  ```cpp
  // BAD: Resource leak on exception
  void risky_function() {
      int* data = new int[1000];
      complex_operation_that_might_throw(data);
      delete[] data;  // Never reached if exception thrown
  }
  
  // GOOD: RAII ensures cleanup
  void safe_function() {
      std::vector<int> data(1000);
      complex_operation_that_might_throw(data.data());
      // Automatic cleanup even if exception occurs
  }
  ```

- **Strong exception safety**: Partial state changes on exceptions
- **No-throw guarantees**: Functions marked `noexcept` that can throw
- **Exception specifications**: Overly broad exception handling

### Error Code Handling

- **Ignored return values**: Not checking function return codes
  ```cpp
  // BAD: Ignoring return value
  fopen("config.txt", "r");  // May fail
  
  // GOOD: Check return value
  FILE* file = fopen("config.txt", "r");
  if (!file) {
      handle_error("Cannot open config file");
      return;
  }
  ```

- **Incomplete error propagation**: Errors not properly propagated up the call stack
- **Inconsistent error handling**: Mixing exceptions and error codes inconsistently
- **Error masking**: Catching exceptions without proper handling

### Input Validation

- **Null pointer checks**: Missing null pointer validation
- **Range validation**: Not validating array indices, container sizes
- **Type validation**: Assuming input is of correct type/form
- **Boundary conditions**: Not handling edge cases (empty input, max values)

## Modern C++ Usage

### C++11/14 Features

- **Auto keyword**: Not using `auto` for type deduction and readability
  ```cpp
  // OLD: Verbose type declarations
  std::map<std::string, std::vector<int>>::iterator it = container.find(key);
  
  // MODERN: Using auto
  auto it = container.find(key);
  ```

- **Range-based for loops**: Using iterator loops instead of range-based for
- **Lambda expressions**: Not using lambdas for local function objects
- **Smart pointers**: Still using raw pointers with manual memory management
- **Move semantics**: Not taking advantage of move semantics

### C++17/20 Features

- **std::optional**: Using sentinel values instead of optional
  ```cpp
  // OLD: Using special values
  int find_value(const std::map<std::string, int>& m, const std::string& key) {
      auto it = m.find(key);
      return (it != m.end()) ? it->second : -1;  // -1 as sentinel
  }
  
  // MODERN: Using optional
  std::optional<int> find_value(const std::map<std::string, int>& m, const std::string& key) {
      auto it = m.find(key);
      if (it != m.end()) {
          return it->second;
      }
      return std::nullopt;
  }
  ```

- **std::variant**: Not using variant for type-safe unions
- **std::string_view**: Unnecessary string copies for read-only operations
- **Concepts**: Using SFINAE when concepts would be clearer
- **RANGES**: Not using range algorithms when appropriate

### Anti-Patterns to Avoid

| Anti-pattern | Modern Alternative |
|--------------|-------------------|
| `NULL` macro | `nullptr` |
| C-style arrays | `std::array` or `std::vector` |
| `malloc/free` | `new/delete` with RAII |
| Manual memory management | Smart pointers |
| C-style casts | `static_cast`, `dynamic_cast`, `const_cast`, `reinterpret_cast` |
| Variadic functions without type safety | Variadic templates or `std::format` |

## Code Structure & Design

### Class Design

- **Rule of Three/Five/Zero**: Not following proper rule for special member functions
  ```cpp
  // BAD: Manual dynamic memory without following Rule of Three
  class BadClass {
  public:
      BadClass() : data(new int[100]) {}
      ~BadClass() { delete[] data; }  // Missing copy constructor/assignment
      
  private:
      int* data;
  };
  
  // GOOD: Following Rule of Zero with smart pointers
  class GoodClass {
  public:
      GoodClass() : data(std::make_unique<int[]>(100)) {}
      // Defaulted special members work correctly
      
  private:
      std::unique_ptr<int[]> data;
  };
  ```

- **Const correctness**: Not marking methods `const` when they don't modify state
- **Encapsulation violations**: Exposing internal state through public members
- **Virtual destructors**: Missing virtual destructors in base classes with virtual functions

### Template Design

- **Template bloat**: Excessive template instantiation
- **Poor error messages**: Unhelpful template error diagnostics
- **SFINAE vs Concepts**: Using complex SFINAE when concepts would be clearer
- **Template recursion**: Deep template recursion causing compilation issues

### Interface Design

- **Clear contracts**: Interfaces don't clearly express pre/post conditions
- **Exception specifications**: Not documenting which exceptions functions can throw
- **Resource ownership**: Unclear who owns returned resources
- **Lifetime management**: Unclear object lifetime expectations

## Build & Compilation

### Compiler Warnings

- **Wall flags**: Not compiling with comprehensive warning flags
  ```bash
  # Recommended warning flags for GCC/Clang
  -Wall -Wextra -Wpedantic -Werror
  -Wconversion -Wsign-conversion
  -Wold-style-cast -Wunused
  ```

- **Warning suppression**: Ignoring specific warnings without justification
- **Warning as errors**: Not treating warnings as errors in production builds
- **Static analysis**: Not using static analysis tools

### Cross-Platform Issues

- **Endianness**: Code that assumes specific byte order
- **Integer size**: Assuming specific integer sizes (int vs int32_t)
- **Alignment**: Making assumptions about memory alignment
- **Platform APIs**: Using platform-specific APIs without abstractions

## Testing & Maintainability

### Testability

- **Dependency injection**: Hard-coded dependencies making testing difficult
- **Mock interfaces**: Not designing interfaces for easy mocking
- **Side effects**: Functions with multiple side effects are hard to test
- **Global state**: Heavy reliance on global state

### Code Readability

- **Naming conventions**: Inconsistent or unclear naming
- **Function length**: Functions that are too long and do too much
- **Magic numbers**: Unexplained numeric literals in code
  ```cpp
  // BAD: Magic numbers
  if (size > 8192) {
      // Process large buffer
  }
  
  // GOOD: Named constants
  constexpr size_t LARGE_BUFFER_THRESHOLD = 8192;
  if (size > LARGE_BUFFER_THRESHOLD) {
      // Process large buffer
  }
  ```

- **Complex expressions**: Overly complex conditional or arithmetic expressions
- **Comments**: Missing, incorrect, or outdated comments

### Documentation

- **API documentation**: Missing documentation for public interfaces
- **Code comments**: Comments that explain what instead of why
- **Code self-documenting**: Code that requires extensive comments to understand
- **Example usage**: Missing usage examples for complex APIs

## Questions to Ask During Review

1. **Performance**: "What's the time and space complexity of this algorithm?"
2. **Memory usage**: "Does this allocate memory in a hot path?"
3. **Exception safety**: "What happens if an exception occurs here?"
4. **Modern C++**: "Could this be written more idiomatically with modern C++?"
5. **Error handling**: "Are all error conditions properly handled?"
6. **Code clarity**: "Is this code easy to understand and maintain?"
7. **Testability**: "How would you test this function?"
8. **Thread safety**: "Is this code safe to call from multiple threads?"
9. **Resource management**: "Who owns this resource and when is it cleaned up?"
10. **Platform independence**: "Does this code make platform-specific assumptions?"