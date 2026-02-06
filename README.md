# resharper-cli-playground

An ASP.NET Core 10 starter application with intentionally bad formatting to test JetBrains ReSharper CLI tools.

## Project Structure

- **StarterApp** - ASP.NET Core 10 Web API with the following endpoints:
  - `GET /weatherforecast` - Get weather forecast data
  - `GET /api/users` - Get list of users
  - `GET /api/users/{id}` - Get user by ID
  - `POST /api/users` - Create a new user
  - `DELETE /api/users/{id}` - Delete a user by ID

## Intentional Formatting Issues

The code contains the following intentional formatting issues to test the formatter:

1. **Wrong import order** - Imports are not alphabetically sorted
2. **Wrong indentation** - Mixed and incorrect indentation throughout
3. **Unnecessary semicolons** - Extra semicolons in various places
4. **Extra braces** - Unnecessary nested braces in some methods
5. **Inconsistent spacing** - Inconsistent spacing in code

## Building and Running

```bash
# Build the application
cd StarterApp
dotnet build

# Run the application
dotnet run

# Access endpoints
curl http://localhost:5000/weatherforecast
curl http://localhost:5000/api/users
```

## Using ReSharper CLI Tools

The repository has JetBrains.ReSharper.GlobalTools installed as a local dotnet tool.

### Inspect Code

```bash
# Inspect code for issues
dotnet jb inspectcode StarterApp/StarterApp.csproj
```

### Cleanup Code

```bash
# Automatically fix formatting issues
dotnet jb cleanupcode StarterApp/StarterApp.csproj
```

### List Available Commands

```bash
# See available ReSharper commands
dotnet jb
```

## Requirements

- .NET 10 SDK or later
- JetBrains.ReSharper.GlobalTools (installed via dotnet-tools.json)

## CI Workflow Template

A reusable guide for setting up ReSharper CLI + SARIF + reviewdog workflows is available at `.github/workflows/README.md`.
