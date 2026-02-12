# AGENTS.md - C++ Code Review Expert Repository

## Project Overview

This is a specialized C/C++ code review skill repository designed for LLM agents. It contains structured checklists, guidelines, and configuration files but **no traditional build/test/lint tooling**.

## Repository Structure

```
code-review-expert/
├── SKILL.md                 # Main skill documentation and workflow
├── agents/
│   └── agent.yaml          # Agent configuration (display name, description)
├── references/
│   ├── cpp-solid-checklist.md       # C++ design principles and RAII patterns
│   ├── cpp-security-checklist.md    # Memory safety, buffer security, vulnerabilities
│   ├── cpp-quality-checklist.md     # Performance, error handling, modern C++ practices
│   └── removal-plan.md             # Template for code removal planning
└── README.md                  # LLM usage guide and triggering mechanisms
```

## Build/Lint/Test Commands

**This repository has no build system, package.json, or development tooling.**

- No npm scripts or package.json files
- No Makefile or build configuration
- No testing framework (Jest, Vitest, etc.)
- No linting/formatting tools (ESLint, Prettier, etc.)
- No development server commands

### Why No Tooling?

This is a **documentation-only repository** containing:
- Markdown reference documents for C++ code review
- YAML agent configuration for LLM integration
- No executable code to build, test, or lint

## Code Style Guidelines

### File Organization
- Use descriptive, kebab-case filenames (`cpp-solid-checklist.md`, `cpp-quality-checklist.md`)
- Group related files in logical directories (`references/` for checklists)
- Keep main documentation at repository root (`SKILL.md`)
- Use consistent directory structure for maintainability

### Markdown Formatting
- Use ATX-style headers (`# Header`, `## Subheader`)
- Include table of contents for long documents
- Use code blocks with language specification (```cpp, ```yaml)
- Maintain consistent indentation (2 spaces for nested lists)
- Use tables for structured data presentation

### YAML Configuration
- Follow YAML 1.2 syntax
- Use snake_case for configuration keys
- Include comments for complex configurations
- Keep line length under 100 characters when practical

### Documentation Style
- Write clear, actionable documentation
- Use present tense for descriptions
- Include examples and anti-patterns
- Provide "Questions to Ask" sections to guide analysis
- Use consistent severity levels (P0-P3)

### Naming Conventions
- **Files**: kebab-case (`cpp-security-checklist.md`)
- **YAML keys**: snake_case (`display_name`, `short_description`)
- **Headers**: Title Case with consistent spacing
- **References**: Use descriptive names that indicate purpose

### C++ Code Examples in Documentation
- Use realistic, production-ready examples
- Show both anti-patterns and correct patterns
- Include language specifications in code blocks
- Add inline comments for complex examples
- Use consistent variable names in examples

## Working with This Repository

### For C++ Code Review Tasks
1. Start with `SKILL.md` to understand the workflow
2. Reference appropriate checklists in `references/`
3. Use the severity framework (P0-P3) for consistent prioritization
4. Follow the structured output format in `SKILL.md`

### For LLM Integration
- Load `agents/agent.yaml` to get display name and description
- Use trigger keywords from `README.md` for automatic skill activation
- Follow the conversation examples for consistent user interaction
- Apply the severity levels when prioritizing issues

### For Modifying Guidelines
1. Update relevant checklist in `references/`
2. Ensure consistency across related documents
3. Update `SKILL.md` if workflow changes
4. Maintain structured format for LLM agent compatibility

### Agent Configuration
- Modify `agents/agent.yaml` to update display name or description
- Keep agent-agnostic (provider-agnostic) configuration
- Use clear, concise descriptions that indicate capabilities

## Important Notes

- **No dependencies**: This repo has no package.json, npm, or external dependencies
- **No build process**: Files are used as-is by the agent system
- **Version control**: Consider versioning changes to checklists carefully
- **Cross-platform**: All files are text-based and work on any platform

## Tool Integration

When integrating with code review tools or IDEs:
- Reference the checklists in `references/` for automated rule creation
- Use the severity framework for consistent issue prioritization
- Map findings to the structured output format in `SKILL.md`
- Consider the workflow steps when designing automated review processes

## LLM Usage Guidelines

### Triggering the Skill
The skill automatically triggers when users mention C++ code review related keywords:

**Direct Review Requests**:
- "Review this C++ code", "Check code quality", "Is this code safe?"

**Security Concerns**:
- "Memory leak", "Buffer overflow", "Security vulnerability", "Race condition"

**Performance Queries**:
- "Performance optimization", "Can this be optimized?", "Algorithm complexity"

**Modernization Needs**:
- "Modern C++", "C++11", "Upgrade code", "Smart pointers"

### Execution Context
Before calling the skill, ensure:
- Complete code snippets with headers
- Clear user intent (review, security, performance, modernization)
- Sufficient context to understand code purpose
- Language confirmation (C/C++ vs other languages)

### Output Format
The skill returns structured reviews with:
- P0-P3 severity classification
- File and line references
- Specific issue descriptions
- Actionable fix suggestions with code examples
- Memory/resource analysis summary
- Additional modernization suggestions

## Best Practices for Agents

### Code Review Workflow
1. **Preflight**: Validate code completeness and user intent
2. **Memory Safety**: Check buffer management, RAII compliance
3. **Security Analysis**: Look for vulnerabilities, undefined behavior
4. **Performance Review**: Identify optimization opportunities
5. **Modernization**: Suggest C++11/14/17/20 improvements
6. **Structured Output**: Present findings in severity-ordered format

### Interaction Patterns
- Use the conversation examples from `README.md` as templates
- Ask clarifying questions when context is insufficient
- Provide concrete code examples for fixes
- Explain technical principles behind suggested improvements
- Follow up on implementation offers appropriately

### Quality Assurance
- Verify all P0/P1 issues are clearly explained
- Ensure code examples compile and follow modern practices
- Check that fix suggestions address root causes
- Provide step-by-step implementation guidance when needed

This repository serves as a comprehensive knowledge base for C++ code review expertise, emphasizing structured analysis, security awareness, and modern C++ best practices for LLM agents.