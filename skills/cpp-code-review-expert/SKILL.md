---
name: cpp-code-review-expert
description: "Expert C/C++ code review with senior engineer lens. Detects memory safety issues, security vulnerabilities, undefined behavior, and architectural problems. Follows modern C++ best practices and RAII principles."
---

# C/C++ Code Review Expert

## Overview

Perform a structured review of current git changes with focus on C/C++ specific concerns: memory management, buffer safety, undefined behavior, resource leaks, concurrency issues, and modern C++ practices. Default to review-only output unless the user asks to implement changes.

## Severity Levels

| Level | Name | Description | Action |
|-------|------|-------------|--------|
| **P0** | Critical | Memory corruption, buffer overflow, use-after-free, undefined behavior, security vulnerability | Must block merge |
| **P1** | High | Memory leak, resource leak, race condition, significant performance regression, non-RAII code | Should fix before merge |
| **P2** | Medium | Poor memory management patterns, unsafe casts, missing error handling, outdated C++ practices | Fix in this PR or create follow-up |
| **P3** | Low | Code style, naming, minor modernization opportunities | Optional improvement |

## Workflow

### 1) Preflight context

- Use `git status -sb`, `git diff --stat`, and `git diff` to scope changes.
- If needed, use `rg` or `grep` to find related modules, usages, and contracts.
- Identify critical paths: memory allocation, I/O operations, parsing, serialization, threading.

**Edge cases:**
- **No changes**: If `git diff` is empty, inform user and ask if they want to review staged changes or a specific commit range,or the entire project.
- **Large diff (>500 lines)**: Summarize by file first, then review in batches by module/feature area.
- **Mixed concerns**: Group findings by logical feature, not just file order.

### 2) Architecture & SOLID Design Review

- Load `references/cpp-solid-checklist.md` for specific prompts.
- Look for:
  - **System Architecture**: Modularity, scalability, maintainability, testability
  - **Component Design**: Cohesion, coupling, interface design, dependency direction
  - **Data Flow Architecture**: Ownership patterns, immutability, event-driven design
  - **Performance Architecture**: Concurrency model, resource pooling, caching strategy
  - **SOLID Principles**: SRP, OCP, LSP, ISP, DIP in architectural context
  - **Design Patterns**: Factory, Strategy, Observer, Command, Repository patterns
  - **Integration Architecture**: API design, message queues, service discovery, circuit breakers
- When you propose architectural changes, explain *why* it improves system qualities.
- If refactor is significant architectural change, propose an incremental migration plan.

### 3) Memory Safety & Resource Management

- Look for:
  - **Memory Management**: Raw pointers vs smart pointers, new/delete pairs, malloc/free mismatches
  - **RAII Violations**: Resources not managed by objects, manual cleanup in destructors
  - **Buffer Safety**: Buffer overflows, out-of-bounds access, missing null terminators
  - **Smart Pointer Usage**: appropriate use of unique_ptr, shared_ptr, weak_ptr
- When you propose memory management fixes, explain *why* RAII is safer and how to apply it.
- If refactor is non-trivial, propose an incremental plan with careful resource ownership transfer.

### 3) Security & Undefined Behavior

- Load `references/cpp-security-checklist.md` for coverage.
- Check for:
  - **Buffer Security**: Buffer overflows, out-of-bounds access, missing null terminators
  - **Integer Security**: Integer overflow/underflow, signed/unsigned conversion issues
  - **Pointer Safety**: Dangling pointers, null pointer dereference, use-after-move
  - **Undefined Behavior**: Uninitialized variables, data races, strict aliasing violations
  - **Input Validation**: Missing bounds checks on user input, unsafe parsing
- Call out both **exploitability** and **impact**.

### 4) Performance & Code Quality

- Load `references/cpp-quality-checklist.md` for coverage.
- Check for:
  - **Performance**: Unnecessary copies, missing move semantics, inefficient algorithms
  - **Error Handling**: Missing return value checks, exceptions vs error codes, errno handling
  - **Compiler Warnings**: Ignored warnings that could indicate real issues
  - **Modern C++**: Using outdated patterns when modern alternatives exist
  - **Template Usage**: Template bloat, inappropriate template instantiation
- Flag issues that may cause crashes, memory corruption, or performance problems.

### 5) Build & Configuration Review

- Check for:
  - **Build System**: Missing dependencies, proper compiler flags (-Wall -Wextra -Werror)
  - **Header Guards**: Include guards or #pragma once in headers
  - **Linkage Issues**: Proper extern declarations, one definition rule violations
  - **Platform Issues**: Endianness, alignment, platform-specific assumptions

### 6) Output format

### 6) Output format

Structure your review as follows:

```markdown
## C++ Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]

---

## Findings

### P0 - Critical (Memory Safety & Security)
(none or list)

### P1 - High (Architecture & Resource Management)
- **[file:line]** Brief title
  - Description of issue
  - Suggested fix

### P2 - Medium (Code Quality & Architecture)
...

### P3 - Low (Style & Minor Architectural Issues)
...

---

## Architecture Analysis
(if applicable - summary of architectural improvements and design concerns)

## Memory/Resource Analysis
(if applicable - summary of memory safety improvements)

## Additional Suggestions
(optional improvements, not blocking)
```

**Inline comments**: Use this format for file-specific findings:
```
::code-comment{file="path/to/file.cpp" line="42" severity="P1"}
Description of issue and suggested fix.
::
```

**Clean review**: If no issues found, explicitly state:
- What was checked (memory safety, RAII compliance, security)
- Any areas not covered (e.g., "Did not verify runtime behavior under load")
- Residual risks or recommended follow-up testing

### 7) Next steps confirmation

After presenting findings, ask user how to proceed:

```markdown
---

## Next Steps

I found X issues (P0: _, P1: _, P2: _, P3: _).

**How would you like to proceed?**

1. **Fix all** - I'll implement all suggested fixes
2. **Fix P0/P1 only** - Address critical and high priority issues
3. **Fix specific items** - Tell me which issues to fix
4. **No changes** - Review complete, no implementation needed

Please choose an option or provide specific instructions.
```

**Important**: Do NOT implement any changes until user explicitly confirms. This is a review-first workflow.

## Resources

### references/

| File | Purpose |
|------|---------|
| `cpp-solid-checklist.md` | C++ design principles and RAII patterns |
| `cpp-security-checklist.md` | Memory safety, buffer security, and vulnerability checklist |
| `cpp-quality-checklist.md` | Performance, error handling, and modern C++ practices |
| `removal-plan.md` | Template for deletion candidates and follow-up plan |
