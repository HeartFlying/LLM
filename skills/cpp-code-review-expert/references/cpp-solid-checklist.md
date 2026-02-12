# C++ Design Principles & Architecture Checklist

## SOLID Principles in C++

## Architecture Design Assessment

### System-Level Architecture

- **Layer separation**: Clear separation between presentation, business logic, and data access layers
- **Module boundaries**: Well-defined interfaces between modules with minimal coupling
- **Dependency direction**: Dependencies should point inward (high-level depends on low-level, not reverse)
- **Scalability considerations**: Design supports horizontal and vertical scaling
- **Plugin architecture**: Extensible design for adding new capabilities without core changes

### Component Design

- **Cohesion**: High cohesion within components (related functionality grouped together)
- **Coupling**: Low coupling between components (minimal dependencies)
- **Interface design**: Stable, minimal interfaces that hide implementation details
- **Component lifecycle**: Clear initialization, usage, and cleanup phases
- **State management**: Proper state handling in stateful components

### Data Flow Architecture

- **Data ownership**: Clear ownership boundaries and transfer mechanisms
- **Immutability**: Prefer immutable data structures where appropriate
- **Event-driven design**: Proper event sourcing or message passing patterns
- **CQRS patterns**: Command Query Responsibility Segregation when applicable
- **Data consistency**: Mechanisms to ensure data consistency across components

### Performance Architecture

- **Concurrency model**: Appropriate use of threading, async, or event-driven patterns
- **Resource pooling**: Proper pooling for expensive resources (connections, threads, memory)
- **Caching strategy**: Appropriate caching levels and invalidation strategies
- **Batching**: Efficient batching of operations to reduce overhead
- **Lazy loading**: On-demand loading to improve startup performance

## SOLID Principles in C++

### SRP (Single Responsibility Principle)

- **Class responsibility**: Classes should have one reason to change (e.g., not mixing UI logic with data persistence)
- **Function scope**: Functions should do one thing well (e.g., not both parsing and validation)
- **Header organization**: Headers should contain only related functionality
- **Resource ownership**: Each resource should have exactly one owner
- **Ask**: "What is the single responsibility of this class/function?"

### OCP (Open/Closed Principle)

- **Template design**: Use templates to extend behavior without modifying existing code
- **Strategy pattern**: Use polymorphism or function objects for extensible behavior
- **Configuration**: Externalize behavior changes via configuration rather than code changes
- **Extension points**: Design interfaces that allow new functionality without core changes
- **Ask**: "Can I add new behavior without touching existing code?"

### LSP (Liskov Substitution Principle)

- **Virtual destructors**: Base classes with virtual functions must have virtual destructors
- **Exception safety**: Derived classes should not strengthen preconditions or weaken postconditions
- **Override correctness**: Overridden functions should maintain base class contracts
- **Covariance**: Return types should be covariant, parameters contravariant
- **Ask**: "Can I substitute any derived class without the caller knowing?"

### ISP (Interface Segregation)

- **Pure virtual classes**: Keep interfaces focused and minimal
- **Multiple inheritance**: Use interface segregation to avoid bloated base classes
- **Template parameters**: Design template interfaces with minimal requirements
- **Function objects**: Prefer small, focused function objects over large interfaces
- **Ask**: "Do all implementers use all methods in this interface?"

### DIP (Dependency Inversion Principle)

- **Abstract interfaces**: Depend on abstract interfaces, not concrete implementations
- **Dependency injection**: Use constructor injection or template parameters
- **Factory patterns**: Use factories to create concrete implementations
- **PIMPL**: Use pointer to implementation for decoupling
- **Ask**: "Can I swap the implementation without changing the client code?"

---

## RAII (Resource Acquisition Is Initialization)

### Core RAII Patterns

- **Smart pointers**: Use `std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr` instead of raw pointers
- **File handles**: Use `std::fstream` or RAII wrappers instead of raw FILE*
- **Mutex locking**: Use `std::lock_guard` or `std::unique_lock` for automatic unlocking
- **Memory allocation**: Encapsulate `new/delete` in RAII classes
- **Resource wrapping**: Wrap any external resource (sockets, database connections) in RAII classes

### Anti-patterns to Avoid

```cpp
// BAD: Manual resource management
void bad_example() {
    char* buffer = new char[1024];
    FILE* file = fopen("data.txt", "r");
    
    // ... use resources ...
    
    delete[] buffer;  // Might not reach if exception occurs
    fclose(file);
}

// GOOD: RAII approach
void good_example() {
    std::vector<char> buffer(1024);
    std::ifstream file("data.txt");
    
    // ... use resources ...
    
    // Automatic cleanup when function exits
}
### Architecture Refactoring Anti-patterns

| Anti-pattern | Description | Better Approach |
|--------------|------------|---------------|
| **God Object** | Classes with too many responsibilities | Split into focused components |
| **Spaghetti Code** | Tangled control flow and dependencies | Clear separation and dependency injection |
| **Copy-Paste Programming** | Duplicated code with slight variations | Extract common functionality |
| **Premature Optimization** | Optimizing without profiling evidence | Measure first, then optimize |
| **Architecture Astronaut** | Over-engineering for future needs | YAGNI - You Aren't Gonna Need It |

---

## Refactoring Heuristics for C++

1. **Encapsulate resources**: Wrap any resource requiring manual cleanup in RAII
2. **Prefer composition over inheritance**: Inheritance creates tight coupling and slicing issues
3. **Use the STL**: Leverage standard containers and algorithms instead of reinventing
4. **Make illegal states unrepresentable**: Use types to enforce invariants
5. **Follow the Rule of Zero/Five**: If you define any of destructor/copy/move, define all
6. **Use const correctness**: Mark methods and parameters `const` when possible
7. **Prefer enums over magic numbers**: Use `enum class` for type-safe constants
8. **Avoid raw loops**: Use STL algorithms and range-based for loops
9. **Use nullptr**: Replace `NULL` with `nullptr` for type safety
10. **Exception safety first**: Design classes with exception safety from the start