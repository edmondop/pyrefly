# Pyrefly Developer Documentation

Welcome to the Pyrefly developer documentation! This guide will help you understand the architecture and contribute effectively to the codebase.

## Quick Navigation

- **[Architecture Guide](architecture-guide.md)** - Complete guide to understanding and modifying Pyrefly
- **[Crate Structure](crate-structure.md)** - Detailed breakdown of the crate organization

## Quick Start for Contributors

### I Want to Add a Type Checking Rule

**Answer**: Your code goes in `pyrefly/lib/alt/solve.rs` or related files in `pyrefly/lib/alt/`

**Read**: [Architecture Guide - Type Solving Section](architecture-guide.md#understanding-type-solving-architecture)

### I Want to Understand How X Works

**For Python language features**: Check `pyrefly_python/` crate
**For type definitions**: Check `pyrefly_types/` crate
**For type checking logic**: Check `pyrefly/lib/alt/`
**For parsing**: Check `pyrefly/lib/module/parse.rs`

**Read**: [Crate Structure Guide](crate-structure.md)

### I Want to Fix a Bug Like the Final Inheritance Issue

**Read**: [Architecture Guide - Implementing the Fix](architecture-guide.md#implementing-the-fix)

## Key Concepts

### The 4-Phase Pipeline

All Python code flows through these phases:
1. **Parsing** - Text → AST
2. **Exports** - Module symbol resolution
3. **Bindings** - AST → Internal representation
4. **Solving** - Type checking (where you probably want to add code)

### TypeOrder: Your Best Friend

When writing type checking code, `TypeOrder` is how you access everything:

```rust
// Access class metadata (including @final decorators)
let metadata = self.type_order.get_metadata_for_class(class);
if metadata.is_final() { /* handle final class */ }

// Check inheritance
if self.type_order.has_superclass(child, parent) { /* valid inheritance */ }

// Access standard library
let object_type = self.type_order.stdlib().object();
```

Think of `TypeOrder` as your "database connection" for type information.

## Common Development Tasks

### Adding New Type Checking Rules
1. Identify which phase needs the change (usually Phase 3: Solving)
2. Find the appropriate file in `pyrefly/lib/alt/`
3. Use `TypeOrder` to access type information
4. Add test cases in `pyrefly/lib/test/`

### Understanding Existing Code
1. Start with the [Architecture Guide](architecture-guide.md) to understand the big picture
2. Use the [Crate Structure](crate-structure.md) to find the right crate
3. Follow the data flow through the 4 phases

### Debugging Type Checking Issues
1. Add debug prints: `dbg!(&some_type);`
2. Run single tests: `cargo test my_test -- --nocapture`
3. Check what metadata is available: `dbg!(self.type_order.get_metadata_for_class(cls));`

## Architecture Highlights

### Clean Layered Design
- **Infrastructure** (`pyrefly_util`, `pyrefly_derive`) - Generic utilities
- **Domain** (`pyrefly_types`, `pyrefly_python`) - Python-specific knowledge
- **Application** (`pyrefly`) - Type checking implementation

### Data Preservation
Annotations like `@final` and `Final[T]` are preserved through all phases:
- **Parsing**: AST nodes
- **Bindings**: `FunctionKind::Final` and `Qualifier::Final`
- **Solving**: Accessible via `TypeOrder`

### Performance Focus
- Rust enables parallelism and optimization
- Module-level incrementality for large codebases
- Efficient AST representation using Ruff parser

## Getting Help

1. **Read the guides**: Most questions are answered in the architecture guide
2. **Look at existing code**: Find similar functionality and see how it's implemented
3. **Add debug prints**: Use `dbg!()` to understand what's happening
4. **Write tests**: Tests are the best way to validate your understanding

## Contributing Guidelines

See the main [CONTRIBUTING.md](../../CONTRIBUTING.md) for:
- Setting up the development environment
- Running tests
- Code style guidelines
- Submitting pull requests

The architecture documentation here focuses on understanding the codebase structure, while the main contributing guide covers the development workflow.