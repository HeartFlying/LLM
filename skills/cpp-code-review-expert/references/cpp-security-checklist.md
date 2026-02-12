# C/C++ Security and Memory Safety Checklist

## Memory Safety Vulnerabilities

### Buffer Overflows

- **String functions**: Unsafe functions like `strcpy`, `strcat`, `sprintf`, `gets`
  ```cpp
  // BAD: No bounds checking
  strcpy(dest, src);  // Can overflow dest
  
  // GOOD: Bounded versions
  strncpy(dest, src, sizeof(dest) - 1);
  dest[sizeof(dest) - 1] = '\0';
  
  // BETTER: Modern alternatives
  std::strncpy(dest, src, sizeof(dest));
  snprintf(dest, sizeof(dest), "%s", src);
  ```

- **Array indexing**: Out-of-bounds access without bounds checking
  ```cpp
  // BAD: No bounds check
  int value = array[index];  // index may be out of bounds
  
  // GOOD: Bounds checking
  if (index >= 0 && index < array_size) {
      int value = array[index];
  }
  ```

- **Pointer arithmetic**: Unsafe pointer operations that can go beyond allocated memory
- **Buffer size calculations**: Off-by-one errors in buffer size calculations

### Use-After-Free & Dangling Pointers

- **Double free**: Freeing the same memory twice
- **Use after delete**: Accessing memory after `delete` or `free`
- **Stack pointer return**: Returning pointers to local variables
  ```cpp
  // BAD: Returns pointer to dead stack memory
  int* bad_function() {
      int local = 42;
      return &local;  // Dangling pointer
  }
  
  // GOOD: Returns by value or heap allocation with RAII
  int good_function() {
      return 42;  // Returns by value
  }
  
  std::unique_ptr<int> better_function() {
      return std::make_unique<int>(42);  // RAII managed
  }
  ```

- **Iterator invalidation**: Using iterators after container modification

### Memory Leaks & Resource Leaks

- **Unbalanced allocation**: `new` without `delete`, `malloc` without `free`
- **Exception paths**: Memory allocated but not freed on exceptions
- **Circular references**: `shared_ptr` cycles preventing garbage collection
- **File handles**: Unclosed files, sockets, or other resources
  ```cpp
  // BAD: Resource leak on exception
  void bad_example() {
      FILE* file = fopen("data.txt", "r");
      char* buffer = new char[1024];
      
      // If exception occurs here, file and buffer leak
      process_data(file, buffer);
      
      delete[] buffer;
      fclose(file);
  }
  
  // GOOD: RAII handles cleanup automatically
  void good_example() {
      std::ifstream file("data.txt");
      std::vector<char> buffer(1024);
      
      process_data(file, buffer.data());
      // Automatic cleanup on any exit path
  }
  ```

## Integer Security

### Integer Overflow & Underflow

- **Arithmetic operations**: Signed integer overflow is undefined behavior
  ```cpp
  // DANGEROUS: Integer overflow (undefined behavior)
  int result = a + b;  // Can overflow
  
  // SAFE: Use wider types or checks
  if (b > INT_MAX - a) {
      // Handle overflow
  }
  long long result = static_cast<long long>(a) + b;
  ```

- **Array size calculations**: Overflow when calculating array sizes
- **Size_t conversion**: Mixing signed and unsigned integers
  ```cpp
  // DANGEROUS: Negative index becomes large positive
  void process_array(int* arr, int size, int index) {
      if (index < size) {  // index might be negative
          arr[index] = value;  // Out of bounds
      }
  }
  ```

- **Signed/unsigned comparison**: Unexpected behavior when comparing signed to unsigned

### Type Conversion Issues

- **Narrowing conversions**: Loss of data in implicit conversions
- **Pointer to integer**: Unsafe pointer truncation
- **Endianness assumptions**: Code that assumes specific byte order
- **Alignment issues**: Misaligned accesses on platforms that require it

## Undefined Behavior

### Uninitialized Variables

- **Local variables**: Using variables before initialization
  ```cpp
  // BAD: Uninitialized variable
  int value;
  if (condition) {
      value = 42;
  }
  use(value);  // value may be uninitialized
  
  // GOOD: Initialize at declaration
  int value = 0;  // Or meaningful default
  if (condition) {
      value = 42;
  }
  use(value);
  ```

- **Class members**: Forgetting to initialize class members in constructors
- **Arrays**: Assuming array elements are zero-initialized

### Strict Aliasing Violations

- **Pointer casting**: Casting between incompatible pointer types
  ```cpp
  // BAD: Strict aliasing violation
  float* f_ptr = new float[10];
  uint32_t* i_ptr = reinterpret_cast<uint32_t*>(f_ptr);
  *i_ptr = 0x3f800000;  // May break optimization
  
  // GOOD: Use memcpy or union (in C++)
  float f = 1.0f;
  uint32_t bits;
  std::memcpy(&bits, &f, sizeof(bits));
  ```

- **Type punning**: Accessing one type as another type

### Data Races & Concurrency Issues

- **Shared data access**: Multiple threads accessing shared data without synchronization
- **Check-then-act**: Non-atomic operations on shared variables
  ```cpp
  // BAD: Race condition
  if (!initialized) {  // Thread A
      initialized = true;  // Thread B might interrupt
      initialize_resource();
  }
  
  // GOOD: Atomic operations or mutex
  std::call_once(init_flag, []() {
      initialize_resource();
  });
  ```

- **Deadlocks**: Incorrect lock ordering leading to deadlocks
- **Memory ordering**: Improper use of atomic operations without correct memory ordering

## Input Validation & Parsing

### String Parsing Security

- **Format string vulnerabilities**: User input in format strings
  ```cpp
  // DANGEROUS: User-controlled format string
  printf(user_input);  // Can lead to arbitrary memory access
  
  // SAFE: Fixed format string
  printf("%s", user_input);
  ```

- **Injection attacks**: SQL injection, command injection through string concatenation
- **Path traversal**: User input in file paths without sanitization
- **Integer parsing**: Unsafe integer parsing without range checks

### Deserialization Issues

- **Buffer overflows**: Deserializing into fixed-size buffers
- **Type confusion**: Trusting type information in serialized data
- **Arbitrary code execution**: Dangerous deserialization patterns
- **Version handling**: Not handling different serialization versions

## Compiler & Build Security

### Compiler Warnings

- **Warning suppression**: Ignoring compiler warnings that may indicate real issues
- **Optimization assumptions**: Code that relies on undefined behavior optimization
- **Stack protection**: Missing stack protection flags (`-fstack-protector`)
- **ASLR**: Missing address space layout randomization

### Build Configuration

- **Debug symbols**: Leaving debug symbols in production builds
- **Hardcoded secrets**: API keys, passwords, or secrets in source code
- **Compiler flags**: Missing security hardening flags
  ```bash
  # Recommended GCC/Clang flags for security
  -Wall -Wextra -Werror -Wformat=2 -Wformat-security
  -fstack-protector-strong -D_FORTIFY_SOURCE=2
  -pie -fPIE  # Position independent executables
  ```

## Cryptography & Randomness

### Random Number Generation

- **Poor randomness**: Using `rand()` or time-based seeds for security
  ```cpp
  // BAD: Predictable random numbers
  srand(time(nullptr));
  int random_value = rand();
  
  // GOOD: Cryptographically secure random
  std::random_device rd;
  std::mt19937 gen(rd());
  std::uniform_int_distribution<> dis(1, 100);
  int random_value = dis(gen);
  ```

- **Key generation**: Using predictable sources for cryptographic keys
- **Seed management**: Reusing or exposing random seeds

### Cryptographic Issues

- **Weak algorithms**: Using deprecated or broken cryptographic algorithms
- **Key management**: Hardcoded keys, improper key storage
- **Implementation errors**: Side-channel vulnerabilities, timing attacks
- **Certificate validation**: Missing or improper certificate verification

## Platform-Specific Issues

### Windows-Specific

- **API usage**: Unsafe Windows API calls (e.g., `strcpy_s` usage errors)
- **Registry access**: Unsecure registry operations
- **Service permissions**: Overly privileged service accounts
- **DLL loading**: Loading DLLs from insecure locations

### Linux/Unix-Specific

- **Setuid programs**: Privilege escalation vulnerabilities in setuid binaries
- **Signal handling**: Unsafe signal handler implementations
- **File permissions**: Incorrect file permissions on sensitive files
- **Shared libraries**: Insecure shared library loading (LD_LIBRARY_PATH issues)

## Questions to Ask During Review

1. **Memory Safety**: "What happens if this allocation fails or this pointer is null?"
2. **Buffer Bounds**: "Are all buffer operations bounds-checked?"
3. **Exception Safety**: "What resources leak if an exception occurs here?"
4. **Data Races**: "Can multiple threads access this data simultaneously?"
5. **Input Validation**: "Is all user input validated and sanitized?"
6. **Integer Operations**: "Can any integer operations overflow or underflow?"
7. **Undefined Behavior**: "Is there any undefined behavior in this code path?"
8. **Resource Management**: "Who owns this resource and when is it cleaned up?"
9. **Error Handling**: "How are errors propagated and handled?"
10. **Platform Dependencies**: "Does this code make assumptions about the platform?"