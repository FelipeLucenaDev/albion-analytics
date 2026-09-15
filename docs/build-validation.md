# Build and Validation Process

## Overview

Albion Analytics uses a structured validation workflow to improve confidence in Windows builds before they are considered ready for manual application testing or release preparation.

The process goes beyond compilation.

It includes:

- Source validation
- Environment checks
- Dependency restore
- Dependency review
- Vulnerability analysis
- Software Bill of Materials generation
- Release compilation
- Automated smoke testing
- Manual Windows QA

The workflow continues to evolve alongside the application.

---

# Validation Philosophy

A successful compilation does not automatically mean that an application is ready to use.

A build can compile while still containing problems related to:

- Dependencies
- Native libraries
- Runtime behavior
- File access
- Persistence
- Environment assumptions
- Security
- Application startup
- Integration between components

For this reason, Albion Analytics treats build validation as a sequence of independent gates.

```text
Source
  ↓
Environment
  ↓
Dependencies
  ↓
Security
  ↓
Build
  ↓
Automated Tests
  ↓
Manual QA
```

Each stage answers a different question about application readiness.

---

# Validation Pipeline

The current process can be represented as:

```text
Repository
    │
    ▼
Pre-Build Assurance
    │
    ▼
Dependency Restore
    │
    ▼
Dependency Audit
    │
    ▼
Vulnerability Validation
    │
    ▼
SBOM Generation
    │
    ▼
Release Build
    │
    ▼
Smoke Tests
    │
    ▼
Manual Windows QA
```

---

# 1. Pre-Build Assurance

Before compiling the application, the repository is checked for conditions that could make the build unreliable or inappropriate.

The pre-build stage may validate areas such as:

- Repository structure
- Expected source state
- Security-related source checks
- Required files
- Platform assumptions
- Build prerequisites

Conceptually:

```text
Repository
    │
    ▼
Pre-Build Checks
    │
    ├── PASS ─────► Continue
    │
    └── FAIL ─────► Investigate
```

The goal is to identify problems before dependency restoration or compilation begins.

---

# 2. Environment Validation

Build behavior can depend on the machine where validation is executed.

Relevant environment information includes:

- Operating system
- .NET SDK
- Runtime architecture
- Required tooling
- Script interpreter availability

For the current Alpha, Windows is the validation target.

Environment validation helps prevent situations where a build failure is incorrectly interpreted as an application defect when the actual cause is a tooling or platform mismatch.

---

# 3. Dependency Restore

The .NET dependency graph is restored before compilation.

Typical command:

```powershell
dotnet restore
```

This stage confirms that the required packages can be resolved using the project's current configuration.

A successful restore verifies package availability but does not guarantee that every dependency is appropriate or secure.

That requires additional validation.

---

# 4. Dependency Audit

Third-party dependencies are explicitly reviewed.

This is important because external packages become part of the application's software supply chain.

The review considers areas such as:

- Package identity
- Native dependencies
- Platform requirements
- Expected usage
- Unexpected components
- Compatibility

During development, dependency validation has already identified components requiring manual review rather than silently accepting the entire dependency tree.

This is intentional.

A dependency should be understood before it is accepted into the validated build.

---

## Manual Dependency Review

Not every package that requires review is necessarily unsafe.

Some legitimate frameworks rely on platform-specific or native dependencies.

When this occurs, the process is:

```text
Dependency flagged
       │
       ▼
Identify package
       │
       ▼
Understand purpose
       │
       ▼
Verify project requirement
       │
       ▼
Accept or correct deliberately
```

The important distinction is that exceptions are documented decisions rather than ignored warnings.

---

# 5. Vulnerability Validation

Dependencies are checked for known security concerns where tooling allows.

The purpose is to reduce the chance of distributing software with known vulnerable components.

The process does not guarantee that software is free from vulnerabilities.

Instead, it adds another validation layer to the release workflow.

```text
Dependency Graph
      │
      ▼
Security Review
      │
      ├── No known issue
      │
      └── Review required
```

Any relevant finding should be investigated before release.

---

# 6. Software Bill of Materials

The validation workflow generates a Software Bill of Materials.

An SBOM provides a structured inventory of software components associated with a build.

For Albion Analytics, the generated artifact is maintained as part of the build-validation output.

Example:

```text
artifacts/
└── sbom.spdx.json
```

The SBOM is useful for:

- Dependency visibility
- Software supply-chain review
- Traceability
- Security investigation
- Future release documentation
- Reproducibility

The project currently uses SBOM generation primarily as an engineering-quality and traceability measure.

---

# 7. Release Build

The application is compiled using the Release configuration.

Typical command:

```powershell
dotnet build -c Release
```

A Release build verifies that the codebase compiles under the configuration intended for distribution and broader validation.

The output of this stage should be evaluated separately from Debug development behavior.

---

## Compilation Failures

Compilation failures are treated as blocking issues.

The preferred workflow is:

```text
Build Failure
     │
     ▼
Identify compiler error
     │
     ▼
Locate affected code
     │
     ▼
Understand the cause
     │
     ▼
Apply targeted correction
     │
     ▼
Build again
```

The project has already encountered real compilation failures during validation.

These were investigated and corrected before continuing the pipeline rather than bypassed. 

---

# 8. Compiler Warnings

A build can succeed while still producing warnings.

Warnings are not automatically treated as equivalent to errors, but they should not be ignored blindly.

Warnings may represent:

- Deprecated APIs
- Future compatibility issues
- Nullability concerns
- Platform behavior
- Library migration requirements

The appropriate response depends on severity and relevance.

A successful Release build therefore means:

```text
Compilation succeeded
```

not necessarily:

```text
No technical debt exists
```

Warnings remain part of the engineering backlog when they require future correction.

---

# 9. Smoke Tests

After compilation, automated smoke tests verify important application behavior.

Smoke tests are intended to answer a basic question:

> Can the application's essential components operate together under expected conditions?

Examples of useful smoke-test coverage include:

- Application services
- Persistence
- SQLite initialization
- Data-processing workflows
- Core integration behavior
- Temporary test environments

Conceptually:

```text
Release Build
     │
     ▼
Smoke Test Environment
     │
     ├── Core workflow A
     ├── Persistence
     ├── Processing
     └── Integration behavior
     │
     ▼
PASS / FAIL
```

---

# 10. Test Failures Are Engineering Evidence

A failed smoke test does not automatically mean the test should be disabled.

Failures are investigated to determine whether the problem exists in:

- Application logic
- Test setup
- File lifecycle
- SQLite lifecycle
- Temporary resources
- Operating-system behavior
- Cleanup logic

During development, smoke testing exposed file-access and SQLite lifecycle behavior that required investigation.

This is exactly the type of problem the validation pipeline is intended to reveal before packaging or release.

---

# 11. Platform-Specific Validation

Windows can behave differently from Unix-like systems in areas such as:

- File locking
- Executable permissions
- Temporary files
- Native dependencies
- Path handling

The validation tooling therefore needs to respect the actual platform being targeted.

A validation rule that makes sense on Linux should not automatically be assumed to behave identically on Windows.

This became relevant while adapting repository and validation checks for the current Windows-first development environment.

---

# 12. Repeat the Pipeline After Corrections

Fixing a single failure is not considered enough.

After a correction, the relevant validation stages are executed again.

```text
Failure
   ↓
Diagnosis
   ↓
Correction
   ↓
Rebuild
   ↓
Retest
   ↓
Continue only after validation
```

This reduces the risk of introducing a second problem while fixing the first one.

---

# 13. Manual Windows QA

Automated validation does not replace actually using the application.

After build and automated checks, the application is manually tested on Windows.

Current manual QA areas include:

- Application startup
- Main interface rendering
- Navigation
- Data import
- Logging
- Persistence behavior
- Error presentation
- Application restart
- User-facing workflow

As the Alpha grows, the manual QA checklist will expand.

---

# 14. Import Validation

The import workflow deserves specific attention because several internal stages can succeed or fail independently.

A useful validation model is:

```text
Input Located
     ↓
Input Read
     ↓
Parsing
     ↓
Validation
     ↓
Transformation
     ↓
Persistence
     ↓
Application State
     ↓
UI Presentation
```

A successful file read alone does not prove that an import succeeded.

Logs and validation need to make it possible to identify the exact stage where the expected result stopped progressing.

---

# 15. Build Artifacts

Generated artifacts should be separated from source code where practical.

Examples include:

```text
artifacts/
├── sbom.spdx.json
└── validation outputs
```

This helps distinguish:

- Source
- Generated evidence
- Build output
- Release packages

Release artifacts should not be confused with the working development repository.

---

# 16. Release Readiness

For the current development stage, a candidate build should ideally satisfy the following categories before being treated as release-ready:

### Source

- Repository validation passes
- Required source files are present
- No unintended temporary changes are included

### Dependencies

- Restore succeeds
- Dependency review is complete
- Relevant security checks pass

### Build

- Release compilation succeeds
- Important warnings are understood

### Validation

- Required automated tests pass
- Critical workflows are manually verified

### Application

- Startup works
- Core offline functionality works
- Import behavior is validated
- Persistence operates correctly
- Logs provide useful diagnostics

---

# 17. Why Build Validation Matters

Albion Analytics combines several technical components:

```text
C#
+
.NET
+
Avalonia
+
SQLite
+
Third-Party Packages
+
Native Dependencies
+
Windows
```

Each component can introduce a different class of failure.

The validation pipeline reduces reliance on the assumption:

> "It works on my machine."

Instead, the objective is to establish repeatable evidence that important parts of the application and its environment are behaving as expected.

---

# Current Validation Principles

The project currently follows these principles:

- Compilation is necessary but not sufficient
- Dependencies should be understood
- Security checks belong in the build process
- Test failures should be investigated
- Platform differences matter
- Generated artifacts should be traceable
- Corrections should be followed by revalidation
- Manual QA remains necessary
- Core workflows take priority during Alpha

---

# Future Improvements

As the project matures, the validation process may evolve to include:

- More automated tests
- Automated packaging validation
- Continuous integration
- Reproducible release artifacts
- Stronger versioning
- Automated UI tests where practical
- Performance validation
- Installation testing
- Upgrade and migration testing
- Formal release checklists

These additions should follow real product requirements rather than being introduced only for complexity.

---

## Related Documentation

- [System Architecture](architecture.md)
- [Engineering Decisions](engineering-decisions.md)
- `development-roadmap.md`

---

## Author

**Felipe Lucena Marcos**

Software Developer  
Software Engineering Student
