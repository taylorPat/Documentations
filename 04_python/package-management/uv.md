# UV

An extremely fast Python package manager which is written in Rust.

- 8–10× faster than pip
- ully compatible with your existing pip workflows
- Actively developed and production-ready

uv is a fast, drop-in replacement for:

🧪 pip (Python package installer)  
🧪 virtualenv / venv (environment creation)  
🧪 pip-tools (dependency resolution and locking)

| Feature                    | uv                        | pip / venv / pip-tools        |
| -------------------------- | ------------------------- | ----------------------------- |
| 💨 Speed                   | 10x faster (Rust-powered) | Slow (Python single-threaded) |
| 🧰 One Tool for All        | venv + install + freeze   | Separate tools                |
| 🧠 Smart Dependency Solver | Built-in                  | Only with pip-tools           |
| 📦 Pip Compatible          | Yes                       | Native                        |
| 🔁 Lockfile support        | Yes (`uv pip compile`)    | Only with pip-tools           |
| 🧼 Clean Interface         | Yes                       | Fragmented                    |

## Installation

```shell
curl -LsSf https://astral.sh/uv/install.sh | sh

# add to PATH
export PATH="$HOME/.local/bin:$PATH"
```

## Use cases

### Projects

Are used for creating application projects (web servers, scripts, cli) or libary projects (packages).

> [!NOTE]  
> Concept reference: https://docs.astral.sh/uv/concepts/projects/  
> Guide reference: https://docs.astral.sh/uv/guides/projects/

#### Setup a project

```shell
# Create a new app project in the current working directory
uv init --app

# pyproject.toml, main.py and .python-version was created
# Check the folder structure
tree .

# run the main.py
uv run main.py

# Using CPython 3.11.4 interpreter at: C:\Python311\python.exe
# Creating virtual environment at: .venv
Hello from task-manager-api!
```

> [!NOTE]  
> The pyproject.toml includes basic metadata.  
> It does not include a build system, it is not a package and it will not be installed into the environment.
> See packaged applications: https://docs.astral.sh/uv/concepts/projects/init/#packaged-applications

> [!NOTE]  
> If you run the main.py for the first time and there is no virtual environment until now, then uv will create a virtual environment inside _.venv_ .

You can also just create a _pyproject.toml_ file by using the `--bare` option (https://docs.astral.sh/uv/concepts/projects/init/#creating-a-minimal-project)

#### Install dependencies

```shell
# add a dependency
uv add "fastapi==0.115.11"
```

```toml
# pyproject.toml
...
dependencies = [
    "fastapi>=0.115.11",
]
```

#### Uninstall dependencies

```shell
# remove dev dependency
uv remove --dev pytest
```

```toml
# pyproject.toml
...
[dependency-groups]
dev = []
```

#### Update dependencies

```shell
uv add "fastapi>=0.115.11"
```

> [!WARNING]  
> The locked version of the dependency will only change if necessary to satisfy the new constraints.
> To force the package version to update to the latest within the constraints, use `uv add "fastapi>=0.115.11" --upgrade-package fastapi`.  
> See https://docs.astral.sh/uv/concepts/projects/dependencies/#changing-dependencies

#### Dev dependencies

Unlike optional dependencies, development dependencies are local-only and will not be included in the project requirements when published to PyPI or other indexes. As such, development dependencies are not included in the `[project]` table.

To add a development dependency, use the `--dev` flag:

```shell
# add dev dependency
uv add --dev pytest
```

```toml
# pyproject.toml
...
[dependency-groups]
dev = [
    "pytest>=8.3.5",
]
```

Development dependencies can be splitted into dependency groups. For example you have a group for testing and for linting and formating.

```shell
# add dev dependency
uv add --group lint ruff
```

```toml
# pyproject.toml
...
[dependency-groups]
dev = [
    "pytest>=8.3.5",
]
lint = [
    "ruff>=0.11.7",
]
```

> [!IMPORTANT]  
> In order to install all depedencies included the ones from the dependency groups use `uv sync --all-groups`.

> [!NOTE]  
> uv requires that all dependency groups are compatible with each other and resolves all groups together when creating the lockfile.  
> If you have dependency groups that conflict with one another, resolution will fail unless you explicitly declare them as conflicting. (https://docs.astral.sh/uv/concepts/projects/config/#conflicting-dependencies)
