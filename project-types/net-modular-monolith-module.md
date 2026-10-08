# Project Type: .NET Modular Monolith Module

Use this template to define a reusable bounded module for an existing .NET modular monolith.

## Description

Creates a module pair for an existing .NET modular monolith solution. The project type creates a contracts library and a module implementation library under `{solutionRoot}/Modules/{ModuleName}` and places both projects in the `./Modules/{ModuleName}` solution folder.

Use this project type when adding a new bounded module to an existing modular monolith and the module should expose a clean public contracts surface while keeping implementation details internal by default.

## Default Standards

List the standards DevCraft should prefer when working in this project type.

- c-standards
- asp-net-core-standards

## Default Architectures

List the architectures DevCraft should consider first for this project type.

- net-modular-monolith
- net-repository-pattern
- net-mediator-pattern-with-xmediat

## DevCraft Execution Scope

Creating a project from this project type is repository setup, not DevCraft feature work.

- Running this project type does not require an active DevCraft feature, and no feature needs to be in the `Implementation` state.
- The DevCraft status-gated implementation rule does not apply while executing the Setup Guidance below. DevCraft may create solutions, projects, folders, project references, package references, configuration, and scaffolded code needed to complete the setup.
- This exemption covers only the setup defined by this project type. Once setup is complete, all further work, including business logic and changes to the generated projects, follows normal DevCraft feature rules.

## Setup Guidance

### Step 1

Ask for the module name and create the module project pair.

- {SolutionName}.{ModuleName}.Contracts: Public integration surface for the bounded module.
  - Type: dotnet-class-library
  - Physical Path: {solutionRoot}/Modules/{ModuleName}/{SolutionName}.{ModuleName}.Contracts
  - Solution Path: Modules/{ModuleName}
- {SolutionName}.{ModuleName}.Module: Internal implementation assembly for the bounded module.
  - Type: dotnet-class-library
  - Physical Path: {solutionRoot}/Modules/{ModuleName}/{SolutionName}.{ModuleName}.Module
  - Solution Path: Modules/{ModuleName}

### Step 2

Apply modular monolith boundaries before adding module behavior.

- Keep contracts public, dependency-light, and free of ASP.NET Core, EF Core, hosting, UI, and module implementation dependencies.
- Keep module implementation types internal by default.
- Expose only deliberate module bootstrap or composition entry points from the module implementation assembly.
- Keep every hand-authored C# type in its own file.
- Do not allow the contracts project to reference the module implementation project.
- Do not allow this module to reference another business module's implementation assembly.

### Step 3

Ask how the module data layer should be organized.

Ask the operator to choose one data access style:

- Repositories: Use repository abstractions and implementations for persistence access.
- Mediator Requests as Commands and Queries: Use mediator messages and handlers for application operations.
- No Data Layer: Do not create persistence-oriented folders or packages.

If the operator chooses Repositories:

- Apply `net-repository-pattern`.
- Create repository-oriented folders in the module implementation project when persistence is used.
- Keep services and handlers persistence-agnostic by depending on repository abstractions instead of directly using EF Core DbContext types.

If the operator chooses Mediator Requests as Commands and Queries:

- Ask which mediator package to use:
  - XMediat
  - MediatR
  - Custom
- If XMediat is selected:
  - Apply `net-mediator-pattern-with-xmediat`.
  - Add `XMediat.Contracts` to the contracts project.
  - Add `XMediat` to the module implementation project.
  - Put command and query contracts in the contracts project.
  - Put command and query handlers in the module implementation project.
- If MediatR is selected:
  - Add the selected MediatR packages according to the repository's package-management conventions.
  - Put command and query contracts in the contracts project.
  - Put command and query handlers in the module implementation project.
- If Custom is selected:
  - Ask the operator for the package name, contract package if separate, runtime package if separate, and required registration guidance before adding packages or folders.

### Step 4

Ask whether the module should use Entity Framework Core.

Only perform this step when the module has persistence.

Ask the operator whether EF Core should be used.

If EF Core is not used:

- Do not add EF Core packages.
- Do not create EF Core DbContext, entity, configuration, or migration folders.

If EF Core is used, ask which database provider should be configured:

- MsSQL
- MySQL
- PostgreSQL
- Sqlite
- Other, user-defined

Then add the libraries necessary for EF Core and the selected provider.

Provider package guidance:

- MsSQL: Microsoft.EntityFrameworkCore.SqlServer
- MySQL: provider selected by the operator, such as Pomelo.EntityFrameworkCore.MySql or Oracle's MySQL provider
- PostgreSQL: Npgsql.EntityFrameworkCore.PostgreSQL
- Sqlite: Microsoft.EntityFrameworkCore.Sqlite
- Other: ask the operator for the exact provider package

### Step 5

For EF Core modules only, ask for the entities to create.

Ask the operator for an entity list where each entity includes its key type.

Expected format:

```text
Person(Guid), Address(int), Phone(int)
```

For each entity in `EntityList`:

- Create one entity class in the module implementation project.
- Place the entity under `DataAccess/Entities`.
- Use the provided key type for the entity identifier.
- Add the entity to the module DbContext.
- Keep each entity class in its own file.

Create or update the EF Core module data structure:

- DataAccess/Entities
- DataAccess/DataContext
- DataAccess/Configurations
- DataAccess/Migrations

Create one hand-authored entity configuration file per entity under `DataAccess/Configurations` when configuration is needed.

### Step 6

Create module composition guidance.

Create a public module composition entry point in the module implementation project.

- {ModuleName}Module: Public module bootstrap or dependency-injection entry point.
  - Type: csharp-class
  - Physical Path: {solutionRoot}/Modules/{ModuleName}/{SolutionName}.{ModuleName}.Module/{ModuleName}Module.cs

The composition entry point should:

- Register module services, repositories, handlers, DbContext, and provider-specific EF Core setup selected during intake.
- Register mediator handlers when a mediator option is selected.
- Avoid business logic.
- Keep implementation collaborators internal.

### Step 7

Create focused validation guidance before module work is considered complete.

- Add or update architecture tests that enforce contracts purity.
- Add or update architecture tests that module implementation types stay internal except deliberate composition entry points.
- Add or update architecture tests that prevent cross-module implementation references.
- If repositories are selected, test that application services and handlers do not bypass repositories with direct DbContext access.
- If EF Core is selected, add an integration test proving important entity constraints and configurations are present in the constructed EF Core model.
- Add one automated test per use case or business outcome.
