# Development Roadmap

## Overview

Albion Analytics is currently under active development toward its first usable **Basic Offline Alpha**.

The initial product strategy is intentionally focused.

The objective is not to implement every planned analytics feature before the first release.

The objective is to establish a stable Windows desktop application with reliable core workflows, local persistence, useful diagnostics and a repeatable validation process.

---

# Current Product Direction

The first release is currently defined around four principles:

- Windows-first
- Offline-first
- Local data processing
- Stable core workflows before advanced features

The Alpha is intended to validate the application's technical foundation before the project expands into broader analytics and product functionality.

---

# Current Stage

**Development Stage:** Basic Offline Alpha  
**Primary Platform:** Windows  
**Application Type:** Desktop  
**Core Technologies:** C# / .NET / Avalonia / SQLite

The project already has a functional software foundation and a validated build process, but the application is still undergoing workflow stabilization and functional QA.

---

# Development Progress

The roadmap can be represented at a high level as:

```text
Project Foundation
       │
       ▼
Desktop Architecture
       │
       ▼
Build & Dependency Validation
       │
       ▼
Core Offline Workflows
       │
       ▼
Basic Offline Alpha
       │
       ▼
Alpha Stabilization
       │
       ▼
Feature Expansion
       │
       ▼
Packaging & Distribution
       │
       ▼
Future Product Evolution
```

---

# Phase 1 — Project Foundation

**Status: Completed / Established**

This phase created the initial technical base for development.

Main areas included:

- .NET project structure
- Avalonia desktop application
- Windows development environment
- Local persistence foundations
- Testing infrastructure
- Build tooling
- Repository validation
- Engineering documentation

The purpose of this stage was to establish a project that could evolve beyond a disposable prototype.

---

# Phase 2 — Build and Supply-Chain Validation

**Status: Established**

A structured validation workflow was introduced before expanding the application further.

This includes:

- Source validation
- Environment validation
- Dependency restore
- Dependency review
- Vulnerability checks
- SBOM generation
- Release compilation
- Automated smoke testing
- Manual validation

The project has already passed important parts of this pipeline during development.

---

## Validated Areas

Current validation work has included:

```text
Pre-Build Assurance
        ✓

Dependency Restore
        ✓

Dependency Review
        ✓

Vulnerability Audit
        ✓

SBOM Generation
        ✓

Release Build
        ✓

Smoke Test Infrastructure
        ✓ / continuing validation

Manual Windows QA
        In progress
```

Validation remains an ongoing activity rather than a one-time milestone.

---

# Phase 3 — Core Offline Application

**Status: In Progress**

This is the current primary development phase.

The objective is to make the application's essential offline workflow reliable.

The main areas are:

- Application startup
- Desktop interface
- Data import
- Data processing
- SQLite persistence
- Logging
- Error handling
- Application state
- Basic analytics presentation

---

# Current Priority — Import Workflow

Data import is currently one of the most important workflows under validation.

A complete import path requires all of the following stages to operate correctly:

```text
Input Source
     │
     ▼
Read
     │
     ▼
Parse
     │
     ▼
Validate
     │
     ▼
Transform
     │
     ▼
Persist
     │
     ▼
Application State
     │
     ▼
UI Presentation
```

A failure at any stage can make the import appear unsuccessful.

For this reason, current work emphasizes observability and clear diagnostics across the entire pipeline.

---

# Basic Offline Alpha Definition

The first Alpha does not require every planned feature.

It requires a dependable minimum product foundation.

A candidate Basic Offline Alpha should provide:

## Application

- Application starts reliably
- Main interface loads correctly
- Core navigation works
- Application can restart without corrupting local state

## Import

- Supported input can be imported
- Invalid input is handled appropriately
- Import failures are logged
- Successful imports reach the expected application state

## Data

- Core data is processed correctly
- SQLite persistence works
- Stored data can be read again
- Database errors do not fail silently

## Diagnostics

- Important workflows generate useful logs
- Errors can be traced to a meaningful stage
- Unexpected failures are diagnosable

## Build

- Release build succeeds
- Dependencies are validated
- Required automated tests pass
- Build artifacts are traceable

## Windows QA

- Application behavior is manually validated on Windows
- Critical workflows operate outside the development IDE
- User-facing failures are understandable

---

# Alpha Exit Criteria

The Basic Offline Alpha can be considered ready when the core workflow is consistently reproducible.

The target flow is:

```text
Install / Launch
       ↓
Application Starts
       ↓
User Imports Data
       ↓
Data Is Parsed
       ↓
Data Is Processed
       ↓
Data Is Stored
       ↓
Results Are Presented
       ↓
Application Can Restart
       ↓
Stored State Remains Valid
```

If this workflow is not reliable, feature expansion should remain secondary.

---

# Phase 4 — Alpha Stabilization

**Status: Planned**

After the core workflow works end-to-end, development will focus on stabilization.

Areas may include:

- Regression fixes
- Import edge cases
- Better error messages
- UI consistency
- Logging improvements
- Database reliability
- Performance review
- Startup robustness
- Test coverage expansion

This phase should reduce known failure modes before the project grows significantly.

---

# Phase 5 — Analytics Expansion

**Status: Planned**

Once the application foundation is stable, analytics capabilities can expand.

Potential areas include:

- Richer statistics
- Historical analysis
- Session analysis
- Filtering
- Comparisons
- Aggregated metrics
- Additional reports
- Improved data visualization

Features should be introduced according to validated user and product requirements.

---

# Phase 6 — User Experience Improvements

**Status: Planned**

As functional stability improves, more attention can shift toward the overall desktop experience.

Potential improvements include:

- More polished navigation
- Better loading states
- Empty-state screens
- Improved error presentation
- Application preferences
- More responsive layouts
- Better data exploration
- Accessibility improvements

The goal is to improve usability without hiding technical errors that still need to be diagnosed.

---

# Phase 7 — Packaging and Distribution

**Status: Planned**

Development execution through the .NET toolchain is not the final distribution experience.

A future release process will need to address:

- Windows packaging
- Installation
- Application versioning
- Clean uninstall behavior
- Local data location
- Upgrade behavior
- Distribution artifacts
- Release notes

The exact packaging strategy will be selected after the core Alpha is stable.

---

# Phase 8 — Release Automation

**Status: Future**

The current validation workflow is already structured enough to support future automation.

Possible evolution includes:

```text
Git Commit
    │
    ▼
Continuous Integration
    │
    ├── Restore
    ├── Security checks
    ├── SBOM
    ├── Build
    └── Tests
    │
    ▼
Release Artifact
```

Continuous integration should be introduced when it improves repeatability rather than simply adding infrastructure.

---

# Phase 9 — Broader Platform Evaluation

**Status: Future**

Avalonia provides cross-platform possibilities, but Linux or other operating systems are not blockers for the first Alpha.

Future evaluation may include:

- Linux build validation
- Linux UI behavior
- Native dependency compatibility
- Packaging differences
- File-system behavior
- Platform-specific QA

Cross-platform support should follow a stable Windows version rather than compete with it during the current stage.

---

# Phase 10 — Optional Connected Services

**Status: Future / Undecided**

The current Alpha does not require cloud infrastructure.

Future product requirements may justify online functionality such as:

- Synchronization
- Shared data
- Accounts
- Remote backup
- Update services
- Online analytics features

These capabilities should not be added until they solve a concrete product problem.

---

# Development Priorities

The current priority order is:

```text
1. Correctness
2. Stability
3. Diagnostics
4. Testability
5. Usability
6. Feature expansion
7. Distribution improvements
8. Advanced product capabilities
```

This priority order may change as the product matures.

---

# What Is Already Established

The project currently has meaningful foundations in:

- C#/.NET development
- Avalonia UI
- SQLite persistence
- Windows build environment
- Release builds
- Dependency auditing
- Vulnerability validation
- SBOM generation
- Smoke testing
- Diagnostic logging
- Repository validation
- Technical documentation

---

# What Is Currently Being Validated

Current work is concentrated around:

- Data import behavior
- Application state after import
- Logging and diagnostics
- Persistence behavior
- End-to-end application workflows
- Manual Windows QA
- Alpha stability

---

# What Is Not Yet a Priority

The following areas are intentionally secondary until the core application is stable:

- Cloud architecture
- User accounts
- Multi-platform release validation
- Advanced dashboards
- Large-scale feature expansion
- Complex deployment infrastructure

This is a scope decision rather than a technical limitation.

---

# Roadmap Philosophy

The roadmap follows several principles.

## Build the Foundation First

A stable core application is more valuable than a large unstable feature set.

## Validate Each Stage

New functionality should not hide failures in existing workflows.

## Keep Scope Explicit

Planned features should not be represented as completed features.

## Allow the Roadmap to Change

Development findings may change technical priorities.

A roadmap is a direction, not a guarantee that every future idea will be implemented exactly as originally imagined.

## Prefer Evidence Over Assumptions

Implementation and testing should determine the next engineering priorities.

---

# Current Milestone

The immediate milestone is:

> **Basic Offline Alpha for Windows**

Success means having a reliable end-to-end local workflow with enough diagnostics and validation to support continued development confidently.

The next milestone should only be defined after the Basic Offline Alpha satisfies its core exit criteria.

---

## Related Documentation

- [System Architecture](architecture.md)
- [Engineering Decisions](engineering-decisions.md)
- [Build and Validation Process](build-validation.md)

---

## Author

**Felipe Lucena Marcos**

Software Developer  
Software Engineering Student
