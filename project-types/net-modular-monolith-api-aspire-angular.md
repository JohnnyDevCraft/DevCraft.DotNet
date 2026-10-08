# Project Type: .NET Modular Monolith API with Aspire and Angular

Use this project type to define a production-ready .NET modular monolith API, hosted through .NET Aspire, with a latest-stable Angular frontend.

## Description

This project type creates or guides a full-stack application with an ASP.NET Core Web API backend, modular monolith backend structure, .NET Aspire AppHost orchestration, Aspire Service Defaults, a shared Aspire constants library, and an Angular application using the latest stable Angular version available at project creation time.

Use this project type when the application should remain a single cohesive backend product with strong internal module boundaries, but still benefit from Aspire orchestration, service defaults, local development composition, and a separate frontend application.

## Default Standards

List the standards DevCraft should prefer when working in this project type.

- c-standards
- asp-net-core-standards
- typescript-standards

## Default Architectures

List the architectures DevCraft should consider first for this project type.

- net-modular-monolith
- net-repository-pattern
- net-aspire-13-3
- angular-frontend
- playwright-testing

## DevCraft Execution Scope

Creating a project from this project type is repository setup, not DevCraft feature work.

- Running this project type does not require an active DevCraft feature, and no feature needs to be in the `Implementation` state.
- The DevCraft status-gated implementation rule does not apply while executing the Setup Guidance below. DevCraft may create solutions, projects, folders, project references, package references, configuration, and scaffolded code needed to complete the setup.
- This exemption covers only the setup defined by this project type. Once setup is complete, all further work, including business logic and changes to the generated projects, follows normal DevCraft feature rules.

## Setup Guidance

### Step 1

Create the solution structure with an Aspire host, shared service defaults, shared Aspire constants, a modular ASP.NET Core API backend, an Angular frontend, and focused automated test projects.

- {SolutionName}.AppHost: Aspire orchestration host for local development and application composition.
  - Type: dotnet-aspire-app-host
  - Physical Path: src/{SolutionName}.AppHost
  - Solution Path: Host
- {SolutionName}.ServiceDefaults: Shared Aspire service defaults for health checks, telemetry, resilience, and service discovery conventions.
  - Type: dotnet-aspire-service-defaults
  - Physical Path: src/{SolutionName}.ServiceDefaults
  - Solution Path: Host
- {SolutionName}.AspireConstants: Shared constants for Aspire resource names, endpoint names, configuration keys, and cross-project orchestration identifiers.
  - Type: dotnet-class-library
  - Physical Path: src/{SolutionName}.AspireConstants
  - Solution Path: Shared
- {SolutionName}.Api: ASP.NET Core Web API entry point for the modular monolith backend.
  - Type: dotnet-aspnet-webapi
  - Physical Path: src/{SolutionName}.Api
  - Solution Path: Backend
- {SolutionName}.Shared: Shared backend contracts and cross-cutting abstractions that are intentionally safe for multiple modules to consume.
  - Type: dotnet-class-library
  - Physical Path: src/{SolutionName}.Shared
  - Solution Path: Backend
- {SolutionName}.{ModuleName}.Module: Backend module implementation assembly. Create one project per bounded module.
  - Type: dotnet-class-library
  - Physical Path: src/Modules/{SolutionName}.{ModuleName}.Module
  - Solution Path: Modules
- {SolutionName}.Web: Angular frontend application using the latest stable Angular version available at project creation time.
  - Type: angular-application
  - Physical Path: src/{SolutionName}.Web
  - Solution Path: Frontend
- {SolutionName}.Api.Tests: Focused API and backend behavior tests.
  - Type: dotnet-test-project
  - Physical Path: tests/{SolutionName}.Api.Tests
  - Solution Path: Tests
- {SolutionName}.Architecture.Tests: Architecture boundary tests for module visibility, dependency direction, repository usage, and other enforced backend constraints.
  - Type: dotnet-test-project
  - Physical Path: tests/{SolutionName}.Architecture.Tests
  - Solution Path: Tests
- {SolutionName}.Web.E2E: Playwright end-to-end tests for frontend workflows.
  - Type: playwright-test-project
  - Physical Path: tests/{SolutionName}.Web.E2E
  - Solution Path: Tests

### Step 2

Apply modular monolith boundaries before adding feature code.

- Keep module implementation types internal by default.
- Expose only deliberate module bootstrap or composition entry points.
- Use the shared backend project only for ownership-neutral contracts and cross-cutting abstractions.
- Keep persistence behind repository abstractions when the repository pattern is used.
- Keep every hand-authored C# type in its own file.

### Step 3

Wire Aspire composition through the AppHost.

- Reference Service Defaults from service projects that participate in Aspire hosting.
- Reference Aspire Constants from AppHost and participating projects that need shared resource names or configuration keys.
- Define stable resource names through the constants library instead of duplicating literal strings across projects.
- Treat AppHost as orchestration and composition, not as a place for business logic.

### Step 4

Create the Angular frontend with current stable Angular tooling.

- Use the latest stable Angular major and patch version available when the project is created.
- Keep Angular CLI and Angular framework package versions aligned.
- Prefer Angular's current first-party patterns for routing, standalone components, signals, forms, and build tooling.
- Put browser workflow tests in the Playwright test project, not inside broad mixed-responsibility scenarios.

### Step 5

Create focused validation coverage before implementation is considered complete.

- Add one automated test per use case or business outcome.
- Keep API, architecture, and Playwright tests separated by responsibility.
- For browser-facing behavior, verify the rendered state after each meaningful action.
- When persistence matters, verify saved state immediately and again after reload or revisit.
- Capture and review screenshots for UI-affecting changes.
