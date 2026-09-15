# Engineering Decisions

## Overview

Albion Analytics is being developed as a Windows-first desktop application focused on local data processing and analytics.

During the current Basic Offline Alpha stage, the main engineering priority is not feature volume.

The priority is building a stable technical foundation that can support future functionality without creating unnecessary complexity too early.

This document describes selected engineering decisions and the reasoning behind them.

---

## 1. Windows-First Instead of Multi-Platform Validation

### Problem

Avalonia supports multiple operating systems, but validating Windows, Linux and potentially macOS simultaneously would increase the amount of testing, packaging and environment-specific debugging required during the earliest development stage.

### Decision

The first usable Alpha targets Windows only.

### Reasoning

The immediate objective is to validate:

- Core application behavior
- Import workflows
- Local persistence
- Desktop UI
- Logging
- Build reproducibility
- Basic analytics functionality

Supporting several platforms before these foundations are stable would expand the validation surface without improving the core product.

### Trade-off

Cross-platform compatibility is not currently guaranteed.

However, using Avalonia preserves the possibility of broader platform support later.

---

## 2. Offline-First Alpha

### Problem

Introducing cloud services early would add additional requirements such as:

- Authentication
- Networking
- Remote infrastructure
- API versioning
- Server deployment
- Availability concerns
- Cloud security

These concerns are not necessary to validate the application's initial analytics workflow.

### Decision

The Basic Alpha operates locally.

The current model is:

```text
Local Input
    │
    ▼
Desktop Application
    │
    ▼
Local Processing
    │
    ▼
SQLite
    │
    ▼
Local Results
```

### Reasoning

An offline-first Alpha provides:

- Lower operational complexity
- Faster iteration
- Easier debugging
- No backend dependency
- Simpler deployment
- Clearer isolation of application problems

### Trade-off

Features requiring shared or cloud-based data are postponed.

This is intentional during the current stage.

---

## 3. SQLite for Local Persistence

### Problem

The application needs structured persistence without requiring users to install and configure a separate database server.

### Decision

SQLite is used for local application persistence.

### Reasoning

SQLite provides:

- Embedded relational storage
- Low deployment complexity
- Structured querying
- Transaction support
- Simple local file management
- Mature .NET ecosystem support

It fits the requirements of an offline desktop Alpha.

### Trade-off

SQLite is not intended to solve every possible future data scenario.

If future requirements involve centralized or highly concurrent storage, persistence architecture may evolve.

---

## 4. Separate UI from Application Logic

### Problem

Desktop applications can become difficult to maintain when UI event handlers accumulate:

- Business rules
- Data processing
- Database access
- File operations
- Error handling

This creates strong coupling between interface code and application behavior.

### Decision

The project aims to keep presentation responsibilities separate from application workflows and domain processing.

Conceptually:

```text
UI
 │
 ▼
Application Workflow
 │
 ▼
Domain / Processing
 │
 ▼
Persistence / Infrastructure
```

### Reasoning

This improves:

- Testability
- Maintainability
- Debugging
- Reusability
- Future UI evolution

### Trade-off

The project contains more abstraction than a simple prototype would require.

That additional structure is accepted because the application is intended to evolve beyond a disposable proof of concept.

---

## 5. Treat Data Import as a Pipeline

### Problem

Data import failures can originate from multiple independent stages.

For example, an import may successfully read a file but still fail because of:

- Parsing
- Validation
- Transformation
- Persistence
- Application state
- UI presentation

### Decision

Import is treated conceptually as a pipeline with identifiable stages.

```text
Source
  ↓
Read
  ↓
Parse
  ↓
Validate
  ↓
Normalize
  ↓
Process
  ↓
Persist
  ↓
Present
```

### Reasoning

This makes failures easier to isolate.

Instead of treating "import failed" as a single problem, diagnostics can identify which stage stopped producing the expected result.

### Result

Logging and testing can be aligned with individual pipeline stages.

---

## 6. Logging as a Core Engineering Feature

### Problem

A desktop application can fail silently or appear to succeed while producing incomplete results.

This is especially difficult during Alpha development because workflows are still changing.

### Decision

Logging is treated as part of the application architecture rather than as an afterthought.

### Important Diagnostic Areas

- Startup
- Import initialization
- File discovery
- Parsing
- Validation
- Database operations
- Application services
- UI workflow
- Unexpected exceptions

### Reasoning

A useful log reduces the distance between:

```text
User-visible symptom
        ↓
Actual failing component
```

This improves both development and future support.

---

## 7. Build Validation Before Packaging

### Problem

A project may compile successfully while still containing issues involving:

- Dependencies
- Vulnerabilities
- Environment assumptions
- Native libraries
- Tests
- Packaging

### Decision

The project uses a broader validation workflow instead of treating `dotnet build` as the only release gate.

A simplified model is:

```text
Source Validation
       ↓
Restore
       ↓
Dependency Review
       ↓
Security Validation
       ↓
SBOM
       ↓
Release Build
       ↓
Tests
       ↓
Manual QA
```

### Reasoning

Successful compilation proves only one part of application readiness.

A release process should also verify the environment and dependency chain.

---

## 8. Dependency Auditing

### Problem

Third-party packages become part of the application's software supply chain.

A build can therefore inherit:

- Vulnerabilities
- Platform requirements
- Native dependencies
- Licensing considerations
- Compatibility issues

### Decision

Dependencies are explicitly reviewed during build validation.

### Reasoning

Dependency management is treated as an engineering responsibility, not only as package installation.

The project has already required investigation of native Avalonia-related dependencies during validation.

### Result

Dependency issues can be documented and deliberately accepted or corrected rather than ignored.

---

## 9. Generating a Software Bill of Materials

### Problem

As dependencies grow, manually tracking every software component included in a build becomes unreliable.

### Decision

The build process generates an SBOM.

### Reasoning

A Software Bill of Materials provides a structured inventory of software components.

This can support:

- Dependency visibility
- Security review
- Release documentation
- Future compliance requirements
- Reproducibility

For the current project, SBOM generation is primarily an engineering and traceability measure.

---

## 10. Automated Tests Plus Manual Desktop QA

### Problem

Automated tests are useful for predictable logic but cannot fully validate every desktop behavior.

Visual layout, interaction and platform-specific behavior may require manual inspection.

### Decision

The project combines:

```text
Automated Validation
        +
Manual Windows QA
```

### Automated Validation

Useful for:

- Core application behavior
- Persistence
- Data processing
- Smoke testing
- Regression checks

### Manual QA

Useful for:

- Window behavior
- UI rendering
- Import workflows
- Visual feedback
- Logging behavior
- Real application lifecycle

### Reasoning

Both methods cover different classes of failure.

---

## 11. Fix Build and Test Problems Instead of Bypassing Them

### Problem

During development it can be tempting to bypass failing tests or validation checks simply to continue building the application.

### Decision

Failures in build or test infrastructure are treated as engineering problems that should be understood.

Examples encountered during development include:

- Compilation errors
- Dependency review issues
- File-locking behavior in smoke tests
- Platform-specific validation differences

### Reasoning

Ignoring validation failures reduces trust in the entire pipeline.

The preferred process is:

```text
Failure
  ↓
Reproduce
  ↓
Understand cause
  ↓
Correct
  ↓
Run validation again
```

---

## 12. Keep Early Architecture Flexible

### Problem

The product is still in Alpha.

Over-engineering early architecture around assumptions that may later change could make development slower.

### Decision

The current architecture defines clear responsibilities without attempting to design every future feature in advance.

### Reasoning

The project should be structured enough to remain maintainable while still allowing discovery during development.

The objective is:

```text
Enough structure
      +
Room to evolve
```

rather than maximum abstraction.

---

## 13. Stabilize Core Workflows Before Expanding Features

### Problem

Adding analytics, dashboards and additional tools is attractive, but doing so before core workflows are stable increases the number of places where failures can occur.

### Decision

Current priorities remain focused on:

1. Startup
2. Import
3. Processing
4. Persistence
5. Diagnostics
6. UI stability
7. Validation
8. Build reproducibility

### Reasoning

Advanced features depend on these foundations.

A larger feature set built on unstable core workflows would create more debugging work later.

---

## 14. Incremental Changes

### Problem

Large changes make it harder to determine which modification introduced a regression.

### Decision

Development favors smaller, validated changes.

### Preferred Cycle

```text
Small Change
    ↓
Build
    ↓
Test
    ↓
Manual Check
    ↓
Next Change
```

### Reasoning

This improves:

- Debugging
- Reviewability
- Regression isolation
- Confidence
- Recovery from failed changes

---

## 15. Treat the Alpha as a Real Software Product

### Problem

An Alpha can easily become a collection of experimental code with no release discipline.

### Decision

Even during development, the project applies practices normally associated with production software.

These include:

- Release builds
- Dependency auditing
- Automated tests
- Logging
- SBOM generation
- Build validation
- Documentation
- Manual QA

### Reasoning

The goal is not to pretend the Alpha is production-ready.

The goal is to build habits and infrastructure that make future production readiness easier to achieve.

---

# Engineering Principles

The current development process follows several practical principles.

## Prefer Simplicity

Do not introduce infrastructure unless it solves a concrete problem.

## Make Failures Observable

A failing workflow should provide enough information to identify where it failed.

## Separate Responsibilities

UI, processing and persistence should not become unnecessarily coupled.

## Validate Assumptions

A successful build does not automatically mean the application behaves correctly.

## Stabilize Before Expanding

Core workflows take priority over feature count.

## Document Important Decisions

Technical decisions should have an explanation, especially when trade-offs are involved.

---

# Lessons from Development

The project has already provided practical experience with:

- Desktop application architecture
- .NET build workflows
- Avalonia UI
- SQLite
- Dependency management
- Software supply-chain awareness
- SBOM generation
- Automated testing
- Windows-specific validation
- Diagnostic logging
- Incremental debugging

As development continues, these decisions may evolve according to evidence gathered during implementation and testing.

---

## Related Documentation

- [System Architecture](architecture.md)
- `build-validation.md`
- `development-roadmap.md`

---

## Author

**Felipe Lucena Marcos**

Software Developer  
Software Engineering Student
