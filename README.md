# uv-workspace-packaging-example

An example repo to showcase how to build out a monorepo using `uv` workspace.

# Steps:

1.  Create a clean repo or folder. No `pyproject.toml` needed, we will create it using `uv`

2.  At the project root folder, run
    ```
    uv init --bare
    ```
    Optionally you can provide a name of the project using `--name`. This will affect the "name" field under `[project]` section in `pyproject.toml`

3.  Create a shared library package:
    ```
    uv init --package --lib commonlib --name my-commonlib
    ```
    This creates a library package `my-commonlib` that can be shared across multiple applications in your monorepo. There will be a new `members` entry under
    `[tool.uv.workspace]` in `pyproject.toml`. In python, you will run `import my_commonlib`.

4.  Create an application package:
    ```
    uv init --package --app app0 --name my-app0
    ```
    This creates an application package that can depend on the shared library. Note that after installation, `my-app0` will be available
    as a callable script in the shell, as specified in `pyproject.toml`

    To add `commonlib` as a dependency, navigate to
    the application directory and add the shared library as a workspace dependency:
    ```
    cd app0/
    uv add "my_commonlib @ workspace:"
    cd ..
    ```

5.  Sync all packages to resolve dependencies:
    ```
    uv sync --all-packages
    ```
    There will be one `uv.lock` file in the monorepo.


6.  (Optional) Build the workspace to ensure everything is properly configured:
    ```
    uv build --all-packages
    ```
    This will build all packages individually. You will find multiple packages under `dist/`, such as
    ```
    dist/
    ├── my_app0-0.1.0-py3-none-any.whl
    ├── my_app0-0.1.0.tar.gz
    ├── my_commonlib-0.1.0-py3-none-any.whl
    └── my_commonlib-0.1.0.tar.gz
    ```

7.  (Optional) Install the packages in editable development mode.
    ```
    uv pip install -e app0/
    ```
    Note that you may need to install each project individually, beacuse
    the root `pyproject.toml` is for a workspace not a set of packages. In
    this case, installing `app0` will install `commonlib` as well, due to the package dependency.


# Project Structure:
After following these steps, your monorepo will have the following structure (not all files are shown):
```
.
├── LICENSE
├── README.md
├── app0
│   ├── README.md
│   ├── pyproject.toml
│   └── src
│       └── my_app0
│           └── __init__.py
├── commonlib
│   ├── README.md
│   ├── pyproject.toml
│   └── src
│       └── my_commonlib
│           └── __init__.py
├── pyproject.toml  # Workspace configuration
└── uv.lock
```
