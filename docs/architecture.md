# System Architecture

## Overview

Albion Analytics is a Windows-first desktop analytics application built with C# and .NET using Avalonia UI.

The project is currently evolving toward a stable Basic Offline Alpha.

Its architecture is designed around local execution, local data processing and local persistence, keeping the first usable version independent from cloud services.

This document describes the current public high-level architecture of the application.

The architecture may evolve as the project progresses beyond the Alpha stage.

---

## Architectural Goals

The current architecture is guided by several goals:

- Keep the initial application usable offline
- Maintain clear separation between UI, application logic and data
- Support reliable local persistence
- Create predictable data-import workflows
- Make failures diagnosable through logging
- Keep components testable
- Support reproducible Windows builds
- Allow future functionality without requiring a complete rewrite

---

# High-Level Architecture

```text
┌──────────────────────────────────────────┐
│                User                      │
└───────────────────┬──────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────┐
│             Avalonia UI                  │
│                                          │
│  • Windows                               │
│  • Views                                 │
│  • User interactions                     │
│  • Application state presentation        │
└───────────────────┬──────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────┐
│          Application Layer               │
│                                          │
│  • Use cases                             │
│  • Import coordination                   │
│  • Validation                            │
│  • Application workflows                 │
└───────────────────┬──────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────┐
│             Domain Logic                 │
│                                          │
│  • Models                                │
│  • Data contracts                        │
│  • Business rules                        │
│  • Data interpretation                   │
└───────────────────┬──────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────┐
│          Local Infrastructure            │
│                                          │
│  • SQLite persistence                    │
│  • File access                           │
│  • Logging                               │
│  • Import sources                        │
└──────────────────────────────────────────┘
```

This diagram represents responsibilities rather than a guarantee that every responsibility is implemented as an independent physical project or assembly.

The internal structure continues to evolve during development.

---

# Desktop UI Layer

The graphical interface is built with Avalonia UI.

The UI layer is responsible for:

- Presenting application data
- Receiving user input
- Starting application workflows
- Displaying import results
- Presenting errors and diagnostics
- Managing desktop windows and views

The UI should avoid becoming the location where domain or persistence rules are implemented.

Conceptually:

```text
User Action
    │
    ▼
Avalonia View
    │
    ▼
Application Workflow
    │
    ▼
Domain / Data Processing
    │
    ▼
Result
    │
    ▼
UI Presentation
```

This separation helps reduce coupling between interface code and application behavior.

---

# Application Layer

The application layer coordinates operations performed by the program.

Examples include:

- Starting an import
- Validating incoming data
- Coordinating data processing
- Requesting persistence operations
- Returning results to the UI
- Handling application-level errors

The purpose of this layer is coordination.

It should not require the UI to understand how data is stored or how individual domain rules are implemented.

---

# Domain Layer

The domain layer represents the information and rules the application works with.

Responsibilities may include:

- Domain models
- Event or payload contracts
- Data normalization
- Validation rules
- Derived values
- Analytics-related transformations

A simplified processing flow is:

```text
Raw Input
    │
    ▼
Parsing
    │
    ▼
Validation
    │
    ▼
Domain Representation
    │
    ▼
Processing
    │
    ▼
Persisted / Presented Result
```

This separation makes it easier to test data-processing behavior independently from the desktop interface.

---

# Import Pipeline

Data import is one of the important workflows in the application.

The import process should behave predictably even when input is incomplete, malformed or unsupported.

A conceptual pipeline is:

```text
Input Source
     │
     ▼
Read Data
     │
     ▼
Parse
     │
     ▼
Validate
     │
     ├── Invalid ──► Diagnostic / Error
     │
     ▼
Normalize
     │
     ▼
Process
     │
     ▼
Persist
     │
     ▼
Update Application State
     │
     ▼
Present Result
```

Each stage should have a clearly defined responsibility.

This makes failures easier to locate.

For example:

```text
File loaded successfully
        +
No records displayed
```

does not automatically mean the UI failed.

The issue could exist in:

```text
Parsing
Validation
Transformation
Persistence
Application state
UI binding
```

The architecture should make these stages observable enough to diagnose such failures.

---

# Local Persistence

The first application version uses local persistence.

SQLite provides a lightweight relational storage system without requiring users to configure a separate database server.

At a high level:

```text
Application
     │
     ▼
Persistence Interface / Service
     │
     ▼
SQLite
     │
     ▼
Local Database File
```

Local persistence is appropriate for the Basic Offline Alpha because it supports:

- Offline execution
- Simple installation
- Local data ownership
- Low operational complexity
- Structured queries
- Future schema evolution

---

## Persistence Responsibilities

Persistence code should be responsible for concerns such as:

- Creating or opening the database
- Reading stored information
- Writing processed information
- Managing schema compatibility
- Handling persistence failures

The UI should not communicate directly with the SQLite database.

---

# Logging and Diagnostics

Logging is an important part of the application because desktop failures can occur at several stages without being immediately visible to the user.

Relevant diagnostic areas include:

```text
Application startup
Import initialization
Input discovery
Parsing
Validation
Database operations
Application services
UI workflows
Unexpected exceptions
```

A useful diagnostic sequence is:

```text
User reports problem
       │
       ▼
Reproduce behavior
       │
       ▼
Inspect application log
       │
       ▼
Identify failing stage
       │
       ▼
Isolate component
       │
       ▼
Implement correction
       │
       ▼
Regression validation
```

Logging therefore serves both development and future production support.

---

# Error Handling

Application errors should be separated according to their purpose.

Examples include:

### Input Errors

Invalid or unsupported user-provided data.

### Persistence Errors

Failures related to local database access or stored data.

### Application Errors

Unexpected failures during application workflows.

### Environment Errors

Problems related to files, permissions, dependencies or runtime environment.

Where possible, technical diagnostics should provide enough information for debugging without exposing unnecessary internal complexity to the end user.

---

# Testing Architecture

Testing is used to validate application behavior independently from manual UI usage.

The project currently includes smoke-test validation as part of its engineering workflow. 

Conceptually:

```text
Application Components
        │
        ├── Domain behavior
        ├── Data processing
        ├── Persistence
        └── Integration behavior
                │
                ▼
          Automated Tests
                │
                ▼
        Expected Behavior
```

Manual application QA remains important for desktop-specific behavior and visual workflows.

---

# Build Architecture

Albion Analytics uses the .NET toolchain for dependency restoration, compilation and execution.

A simplified build path is:

```text
Source Code
    │
    ▼
Environment Validation
    │
    ▼
Dependency Restore
    │
    ▼
Dependency / Security Checks
    │
    ▼
Release Build
    │
    ▼
Automated Validation
    │
    ▼
Manual Windows QA
```

Build validation is treated as part of software quality rather than only as a packaging step.

---

# Dependency Management

Dependencies are reviewed because third-party packages become part of the application's software supply chain.

Relevant checks include:

- Dependency restore
- Package compatibility
- Vulnerability review
- Platform requirements
- Native dependency analysis

The project also generates a Software Bill of Materials (SBOM) as part of its validation workflow. 

This provides a structured record of software components included in a build.

---

# Windows-First Strategy

The current Alpha is intentionally Windows-first.

The priority is:

```text
Stable Windows Alpha
        │
        ▼
Core functionality validated
        │
        ▼
Application workflow stabilized
        │
        ▼
Packaging / distribution improved
        │
        ▼
Additional platform validation later
```

Cross-platform support should not block the first usable Windows version.

Avalonia remains useful because the UI technology allows broader platform possibilities in the future, but the current QA target is Windows. 

---

# Offline-First Alpha

The initial version is designed around local functionality.

This reduces infrastructure requirements during the early product stage.

The current model can be summarized as:

```text
Desktop Application
        +
Local Input
        +
Local Processing
        +
Local Database
        =
Offline Alpha
```

This allows the project to validate its core analytics and application workflows before introducing unnecessary distributed-system complexity.

---

# Application Startup

A conceptual startup sequence is:

```text
Application Start
      │
      ▼
Environment Initialization
      │
      ▼
Configuration
      │
      ▼
Database Initialization
      │
      ▼
Application Services
      │
      ▼
Avalonia UI
      │
      ▼
Ready
```

Startup failures should be logged and surfaced appropriately when they prevent normal application operation.

---

# Architectural Boundaries

The application aims to maintain several important boundaries.

## UI does not own persistence logic

Views should consume application functionality instead of executing database operations directly.

## Persistence does not own business rules

Storage code should focus on storing and retrieving information.

## Domain logic should be testable

Core processing should not require a running desktop interface whenever that dependency can reasonably be avoided.

## Diagnostics cross system boundaries

Each major workflow should provide enough information to identify where a failure occurred.

---

# Current Development Priorities

For the Basic Offline Alpha, architectural priorities are currently:

1. Reliable application startup
2. Functional data import
3. Correct local processing
4. Stable SQLite persistence
5. Useful diagnostics
6. Functional desktop UI
7. Automated validation
8. Reproducible Windows builds

Advanced functionality should not compromise these foundations.

---

# Future Evolution

The architecture may later expand to support areas such as:

- More advanced analytics
- Data visualization
- Additional import formats
- Improved application settings
- Better packaging
- Automatic update mechanisms
- Performance optimization
- Additional operating systems
- Optional online services

These capabilities should be introduced according to concrete product requirements rather than added prematurely.

---

# Architecture Philosophy

The current architecture follows a few practical principles:

- Keep responsibilities identifiable
- Prefer local simplicity during the Alpha
- Make errors diagnosable
- Validate behavior before expanding features
- Avoid unnecessary infrastructure
- Keep core processing testable
- Build around incremental evolution

The objective is not architectural complexity.

The objective is a structure that remains understandable and maintainable as Albion Analytics grows.

---

## Related Documentation

Planned documentation:

- `engineering-decisions.md`
- `build-validation.md`
- `development-roadmap.md`

---

## Author

**Felipe Lucena Marcos**

Software Developer  
Software Engineering Student
