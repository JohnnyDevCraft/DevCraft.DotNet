# DevCraft.DotNet

Reusable [DevCraft](https://github.com/JohnnyDevCraft/DevCraft) guidance for .NET development: architectures, coding standards, and project types.

One command installs everything into your DevCraft profile and registers it in your DevCraft catalog.

## Install

Paste the line for your platform into a terminal.

### macOS / Linux

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/JohnnyDevCraft/DevCraft.DotNet/main/install.sh)"
```

### Windows (PowerShell)

```powershell
irm https://raw.githubusercontent.com/JohnnyDevCraft/DevCraft.DotNet/main/install.ps1 | iex
```

Works in Windows PowerShell 5.1 and PowerShell 7. You don't need to change the execution policy.

When it finishes you'll see something like:

```text
Downloading https://github.com/JohnnyDevCraft/DevCraft.DotNet/archive/refs/heads/main.tar.gz
Library files: 16 copied, 0 unchanged, 0 backed up.
Merging catalog with: devcraft merge
DevCraft.DotNet installed into /Users/you/.DevCraft.
```

## Requirements

- DevCraft must already be installed:
  - The profile file `~/.DevCraft/configure.json` exists (Windows: `%USERPROFILE%\.DevCraft\configure.json`).
  - The `devcraft` command is on your `PATH`, or the binary is in the profile folder.
- Your DevCraft version must support `devcraft merge`.
- macOS/Linux: `curl` and `tar`. Both are present by default.

## What the installer does

1. **Checks** that DevCraft is installed. If it isn't, the installer stops before changing anything.
2. **Downloads** this repository from GitHub into a temporary folder. The folder is deleted afterwards.
3. **Copies** the Markdown files from `architectures/`, `standards/`, `project-types/`, `skills/`, and `templates/` into your profile at the same paths, for example `~/.DevCraft/standards/CSharp.md`.
   - Files that are already identical are skipped.
   - Files that differ are **backed up**, then replaced.
4. **Merges** this repository's `catalog.json` into your profile catalog by running `devcraft merge`. New entries are added and existing entries with the same slug are updated.

The installer exits non-zero if any step fails. If a copy fails, the merge is not run.

Running the installer again is safe, and it's also how you update.

## Backups

Before overwriting a file, the installer saves the previous version to:

```text
~/.DevCraft/backups/install-<yyyyMMdd-HHmmss>/<original path>
```

On Windows this is `%USERPROFILE%\.DevCraft\backups\...`. To undo an update, copy the file back. `devcraft merge` handles backing up `configure.json` itself.

## Inspect before running

If you'd rather read the script before running it:

```bash
curl -fsSL -o install.sh https://raw.githubusercontent.com/JohnnyDevCraft/DevCraft.DotNet/main/install.sh
less install.sh
/bin/bash install.sh
```

```powershell
irm https://raw.githubusercontent.com/JohnnyDevCraft/DevCraft.DotNet/main/install.ps1 -OutFile install.ps1
notepad install.ps1
powershell -NoProfile -ExecutionPolicy Bypass -File .\install.ps1
```

## Options

Set these environment variables before running the installer to change its behavior:

| Variable | Default | Purpose |
|---|---|---|
| `DEVCRAFT_HOME` | `~/.DevCraft` (`%USERPROFILE%\.DevCraft`) | The profile to install into. |
| `DEVCRAFT_SOURCE` | `https://github.com/JohnnyDevCraft/DevCraft.DotNet/archive/refs/heads/main` | Where to install from. Accepts an archive URL, a local archive (`.tar.gz` for bash, `.zip` for PowerShell), or a local folder such as a clone or fork. |

Example: install from a local clone.

```bash
DEVCRAFT_SOURCE="$PWD" ./install.sh
```

```powershell
$env:DEVCRAFT_SOURCE = (Get-Location).Path; .\install.ps1
```

## Troubleshooting

| Message | Fix |
|---|---|
| `DevCraft is not installed: …configure.json was not found.` | Install DevCraft first, or set `DEVCRAFT_HOME` to your profile folder. |
| `DevCraft is not installed: the devcraft binary was not found…` | Add the DevCraft binary to your `PATH`, or place it in the profile folder. |
| `devcraft merge failed (exit N).` | Your DevCraft version may not support `devcraft merge`; update DevCraft and run the installer again. The files are already copied, so a rerun completes the install. |
| `could not download or extract …` | Check your network connection and that GitHub is reachable. |

## What's included

| Type | Slug | Description |
|---|---|---|
| Architecture | `angular-frontend` | Define the default architecture guidance for Angular frontend applications. |
| Architecture | `net-aspire-13-3` | Define the default architecture guidance for building and operating distributed .NET applications with Aspire 13.3+. |
| Architecture | `net-distributed-app` | Define the default architecture guidance for a .NET distributed application made up of multiple deployable parts. |
| Architecture | `net-mediator-pattern` | Define how .NET applications use the mediator pattern through the `Mediator` library. The mediator is the in-process boundary between callers and application behavior: callers express intent as messages, while Mediator locates and invokes matching handlers. |
| Architecture | `net-mediator-pattern-with-xmediat` | Define how .NET applications use the mediator pattern through Xelseor LLC's `XMediat` library. The mediator is the in-process boundary between callers and application behavior: callers express intent as messages, while XMediat locates and invokes the matching handlers. |
| Architecture | `net-micro-services` | Define the default architecture guidance for a .NET microservices system. |
| Architecture | `net-modular-monolith` | Define the default architecture guidance for a .NET modular monolith application. |
| Architecture | `net-n-tier-monolith` | Define the default architecture guidance for a .NET n-tier monolith. |
| Architecture | `net-repository-pattern` | Define a reusable repository pattern for C# and .NET applications that use Entity Framework Core while preserving clear module boundaries, readable query composition, and testable data access. |
| Standard | `asp-net-core-standards` | Define ASP.NET Core specific standards that build on the shared C# standards. |
| Standard | `c-standards` | Define default coding standards for C# code across services, applications, libraries, and tests. |
| Standard | `javascript-standards` | Define default coding standards for JavaScript code in browser, server, scripting, and test environments. |
| Standard | `typescript-standards` | Define default coding standards for TypeScript applications, libraries, frontend code, backend code, and tests. |
| Project type | `project-type-net-modular-monolith-api` | Creates an ASP.NET Core Web API modular monolith without .NET Aspire. This project type is for backend applications that should deploy as one application while preserving explicit module boundaries through contracts projects, module implementation projects, a shared core contracts project, and a shared core module project. |
| Project type | `project-type-net-modular-monolith-api-aspire-angular` | This project type creates or guides a full-stack application with an ASP.NET Core Web API backend, modular monolith backend structure, .NET Aspire AppHost orchestration, Aspire Service Defaults, a shared Aspire constants library, and an Angular application using the latest stable Angular version available at project creation time. |
| Project type | `project-type-net-modular-monolith-module` | Creates a module pair for an existing .NET modular monolith solution. The project type creates a contracts library and a module implementation library under `{solutionRoot}/Modules/{ModuleName}` and places both projects in the `./Modules/{ModuleName}` solution folder. |

## Contributing

1. Add or edit Markdown in `architectures/`, `standards/`, `project-types/`, `skills/`, or `templates/`.
2. Add or update the file's entry in `catalog.json`.
   - Each entry has `Slug`, `Name`, `Description`, and `Path`.
   - Slugs are lowercase kebab-case, and `Path` is relative to the repository root.
   - Every Markdown file in those folders needs exactly one entry.
3. Run the tests:

```bash
tests/catalog.tests.sh        # catalog consistency (requires jq)
tests/install.sh.tests.sh     # install.sh in a sandbox (shellcheck optional)
```

```powershell
Invoke-Pester ./tests/install.ps1.Tests.ps1   # run under Windows PowerShell 5.1 and pwsh 7 (Pester 5)
```
