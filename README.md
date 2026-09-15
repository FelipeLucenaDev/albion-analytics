# Albion Analytics

> Windows-first desktop analytics application built with C#/.NET and Avalonia, currently under active development.

## Overview

Albion Analytics is a desktop application focused on collecting, processing and presenting gameplay-related data through a local desktop interface.

The project is being developed as a practical software engineering project involving desktop application architecture, local persistence, data processing, validation, testing and build automation.

The initial release target is Windows.

This repository is currently used as a public technical case study and development documentation repository.

The production development source code is not yet publicly distributed.

---

## Project Status

**Status:** Active Development  
**Current Stage:** Basic Offline Alpha  
**Initial Platform:** Windows  
**Application Type:** Desktop

The project is still evolving and features, architecture and user interface may change during development.

---

## Objectives

The project is being developed with several technical goals:

- Build a maintainable desktop application architecture
- Process gameplay-related data locally
- Provide a clear and usable desktop interface
- Persist application data locally
- Support reliable data import workflows
- Validate dependencies and build integrity
- Introduce automated testing and release validation
- Establish a repeatable Windows build process

---

## Technology Stack

### Application

`C#` · `.NET`

### User Interface

`Avalonia UI`

### Data

`SQLite`

### Development

`Git` · `GitHub` · `.NET CLI`

### Validation & Tooling

`Automated Tests` · `Build Validation` · `Dependency Auditing` · `SBOM`

---

## Current Architecture

At a high level, the application is structured around several responsibilities:

```text
┌──────────────────────────────┐
│        Desktop UI            │
│        Avalonia              │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│      Application Logic       │
│                              │
│  • Data processing           │
│  • Import workflows          │
│  • Application services      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Local Storage         │
│                              │
│  • SQLite                    │
│  • Local application data    │
└──────────────────────────────┘
```

The architecture will continue to evolve as the application progresses beyond the initial alpha stage.

---

## Engineering Areas

Development of Albion Analytics involves work across several software engineering areas.

### Desktop Application Development

- Application lifecycle
- Window and view management
- UI composition
- Desktop-specific behavior
- Windows validation

### Data Processing

- Input validation
- Data transformation
- Import workflows
- Local persistence

### Software Architecture

- Separation of responsibilities
- Domain modeling
- Application services
- Maintainable project structure

### Build Engineering

- Dependency restore
- Release builds
- Environment validation
- Dependency auditing
- Software Bill of Materials generation

### Quality Assurance

- Automated smoke tests
- Build validation
- Dependency checks
- Manual application testing

---

## Build Validation

The project includes a validation process designed to detect problems before release builds.

A simplified pipeline is:

```text
Source Validation
       ↓
Dependency Restore
       ↓
Dependency Audit
       ↓
Security Checks
       ↓
SBOM Generation
       ↓
Release Build
       ↓
Automated Tests
       ↓
Manual Application QA
```

This process is being improved alongside the application itself.

---

## Development Approach

The application is being developed incrementally.

The current priority is establishing a stable basic offline application before expanding the feature set.

The development process focuses on:

- Small validated changes
- Reproducible builds
- Clear technical diagnostics
- Dependency control
- Regression prevention
- Documented engineering decisions

---

## Current Focus

Current development work is focused on the Windows Basic Offline Alpha.

Areas under active development include:

- Data import
- Local processing
- Application UI
- Logging and diagnostics
- Local persistence
- Stability
- Build and test validation

---

## Planned Evolution

Future stages may include improvements in areas such as:

- Analytics and reporting
- More advanced data visualization
- Improved import workflows
- Extended persistence
- Application settings
- Packaging and distribution
- Performance optimization
- Additional automated tests

The roadmap may change according to development findings and testing results.

---

## Documentation

This repository will progressively include additional documentation covering:

- System architecture
- Engineering decisions
- Development roadmap
- Build and validation process
- Technical challenges
- Release preparation

---

## Repository Scope

This repository currently serves as a **public technical case study**.

It is intended to demonstrate:

- Application architecture
- Engineering practices
- Technologies
- Development progress
- Technical decisions
- Build and validation workflows

Sensitive, experimental or unstable development code is not currently included.

---

## Author

**Felipe Lucena Marcos**

Software Developer  
Software Engineering Student

GitHub: [@FelipeLucenaDev](https://github.com/FelipeLucenaDev)
