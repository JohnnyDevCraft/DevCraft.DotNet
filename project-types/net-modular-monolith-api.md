# Project Type: .NET Modular Monolith API

Use this template to define a type of project that DevCraft can be used to create.

## Description

Creates an ASP.NET Core Web API modular monolith without .NET Aspire. This project type is for backend applications that should deploy as one application while preserving explicit module boundaries through contracts projects, module implementation projects, a shared core contracts project, and a shared core module project.

## Default Standards

List the standards DevCraft should prefer when working in this project type.
- c-standards
- asp-net-core-standards

## Default Architectures

List the architectures DevCraft should consider first for this project type.
- net-modular-monolith

## Setup Guidance

### Step 1

Create the solution structure with an ASP.NET Core Web API host, shared core projects, repeatable bounded module projects, and focused test projects.

- {SolutionName}.Api: ASP.NET Core Web API host and composition root for the modular monolith.
  - Type: dotnet-aspnet-webapi
  - Physical Path: src/Web/{SolutionName}.Api
  - Solution Path: Web
- {SolutionName}.Core.Contracts: Ownership-neutral shared contracts, primitives, constants, common event metadata, and cross-module abstractions.
  - Type: dotnet-class-library
  - Physical Path: src/Modules/Core/{SolutionName}.Core.Contracts
  - Solution Path: Modules/Core
- {SolutionName}.Core.Module: Shared cross-cutting implementation module for reusable infrastructure that is safe across modules.
  - Type: dotnet-class-library
  - Physical Path: src/Modules/Core/{SolutionName}.Core.Module
  - Solution Path: Modules/Core
- {SolutionName}.{ModuleName}.Contracts: Public integration surface for a bounded business module.
  - Type: dotnet-class-library
  - Physical Path: src/Modules/{ModuleName}/{SolutionName}.{ModuleName}.Contracts
  - Solution Path: Modules/{ModuleName}
- {SolutionName}.{ModuleName}.Module: Internal implementation assembly for a bounded business module.
  - Type: dotnet-class-library
  - Physical Path: src/Modules/{ModuleName}/{SolutionName}.{ModuleName}.Module
  - Solution Path: Modules/{ModuleName}
- {SolutionName}.Api.Tests: Focused API and backend behavior tests.
  - Type: dotnet-test-project
  - Physical Path: tests/{SolutionName}.Api.Tests
  - Solution Path: Tests
- {SolutionName}.Architecture.Tests: Architecture boundary tests for project references, module visibility, contract purity, and dependency direction.
  - Type: dotnet-test-project
  - Physical Path: tests/{SolutionName}.Architecture.Tests
  - Solution Path: Tests

### Step 2

Apply modular monolith boundaries before adding feature code.

- Keep the API host thin and use it for HTTP transport, startup, and composition.
- Keep business behavior inside module implementation projects.
- Keep module implementation types internal by default.
- Expose only deliberate module bootstrap or composition entry points from module implementation assemblies.
- Keep contracts free of ASP.NET Core, EF Core, hosting, UI, and module implementation dependencies.
- Keep every hand-authored C# type in its own file.

### Step 3

Create bounded modules using the repeatable contracts/module pair.

- Create one {SolutionName}.{ModuleName}.Contracts project per bounded module.
- Create one {SolutionName}.{ModuleName}.Module project per bounded module.
- Allow the API host to reference module contracts and module composition entry points.
- Do not allow one business module to reference another business module's implementation assembly.
- Use {SolutionName}.Core.Contracts only for ownership-neutral shared contracts.
- Use {SolutionName}.Core.Module only for shared cross-cutting implementation.

### Step 4

Create focused validation coverage before implementation is considered complete.

- Add architecture tests that enforce module dependency direction.
- Add architecture tests that implementation types stay internal except deliberate composition entry points.
- Add API tests for host wiring and endpoint behavior.
- Add one automated test per use case or business outcome.
