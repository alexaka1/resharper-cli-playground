# ReSharper CLI GitHub Workflow Guide

This guide shows how to add a **manual + pull request** workflow that checks code style/formatting issues with **ReSharper CLI InspectCode**, exports **SARIF**, and uses **reviewdog** to publish a PR check.

## Why `inspectcode` for CI checks?

- `inspectcode` is best for **reporting** problems in CI (including formatting/style inspections).
- `cleanupcode` is best for **auto-fixing** issues (usually run locally or in a separate autofix workflow).

## Minimal workflow (manual + PR, SARIF + reviewdog)

Use the workflow in this repo: [`resharper-format-check.yml`](./resharper-format-check.yml).

Key parts:
- Trigger on `pull_request` and `workflow_dispatch`.
- Run `dotnet tool restore` and `dotnet jb inspectcode ... --format=Sarif`.
- Upload SARIF to GitHub code scanning.
- Run reviewdog with `-f=sarif` and `-reporter=github-pr-check`.

## Running InspectCode on one project

```bash
dotnet jb inspectcode StarterApp/StarterApp.csproj --format=Sarif --output=inspectcode.sarif
```

## Running InspectCode on multiple projects

Use one command per project and merge/report as needed.

```bash
dotnet jb inspectcode src/App1/App1.csproj --format=Sarif --output=inspectcode-app1.sarif
dotnet jb inspectcode src/App2/App2.csproj --format=Sarif --output=inspectcode-app2.sarif
```

In GitHub Actions, you can do this with a matrix:

```yaml
strategy:
  matrix:
    project:
      - src/App1/App1.csproj
      - src/App2/App2.csproj
steps:
  - run: dotnet jb inspectcode "${{ matrix.project }}" --format=Sarif --output=inspectcode.sarif
```

## Running InspectCode for all projects

Prefer targeting a solution (`.sln`) so ReSharper analyzes all included projects in one run:

```bash
dotnet jb inspectcode MySolution.sln --format=Sarif --output=inspectcode.sarif
```

If you do not have a solution file, you can:
- create one and include projects, or
- loop over all `*.csproj` files and run inspectcode per project.

## Configuration: `.editorconfig` and JetBrains team settings

### `.editorconfig`

Put `.editorconfig` at repo root (or in subfolders). ReSharper CLI picks up the same code style/severity context used by ReSharper/Rider and Roslyn analyzers when inspecting files under those paths.

### JetBrains team-shared settings

Commit team-shared ReSharper settings in your repo so CI and developers use the same rules:

- `YourSolution.sln.DotSettings` (recommended team-shared layer)
- optional additional layer files if your team uses them

ReSharper CLI loads settings layers from the solution/project context. Keep them versioned in git and avoid committing user-local files like `*.DotSettings.user`.

## Optional: use CleanupCode separately

If you also want auto-fix, add a separate workflow/job:

```bash
dotnet jb cleanupcode MySolution.sln
```

Then either:
- fail CI if `git diff --exit-code` detects required formatting changes, or
- open an automated formatting PR.

## Troubleshooting

- If `dotnet jb` fails, ensure local tools are restored:
  ```bash
  dotnet tool restore
  ```
- If no findings appear in PR checks, verify:
  - SARIF file is generated,
  - reviewdog runs only on `pull_request`,
  - `REVIEWDOG_GITHUB_API_TOKEN` is set to `${{ secrets.GITHUB_TOKEN }}`.
