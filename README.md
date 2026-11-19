# DAQForge

DAQForge is a modular, object-oriented Python framework for data acquisition.

## Open in VS Code

DAQForge uses a Debian Linux dev container. You can open it from Windows, macOS, or Linux with VS Code, the **Dev Containers** extension, and Docker configured to run Linux containers.

- **Windows:** Use Docker Desktop in Linux containers mode. If your checkout is inside WSL 2, enable Docker Desktop integration for that distribution and open the folder in VS Code from WSL.
- **macOS:** Use Docker Desktop for your Mac's architecture. (OrbStack works great too)
- **Linux:** Install and start Docker Engine for your distribution and make sure your user can run Docker commands.

See the [VS Code Dev Containers setup guide](https://code.visualstudio.com/docs/devcontainers/containers#system-requirements) for host requirements.

1. Start Docker and open the repository folder in VS Code.
2. Run **Dev Containers: Reopen in Container** from the Command Palette.
3. The first launch creates a virtual environment at `/opt/daqforge-venv` with `python -m venv` and installs DAQForge in editable mode with its development dependencies. VS Code installs Python, Pylance, and Python Debugger in the container and selects the volume-backed interpreter.

The repository is bind-mounted from your host. The virtual environment lives in a dedicated `daqforge-venv-…` Docker volume for this checkout and survives a container rebuild.

## Develop inside the container

Run these commands from the VS Code terminal. Re-run the install command after changing dependencies.

```sh
python -m pip install -e '.[dev]'
python -m pytest
python -m ruff check .
python -m mypy
python -m build
```

The build writes a wheel and source distribution to `dist/`. To debug the smoke test, use **Debug Test** in VS Code’s Testing view.

## Remove the development environment

Close the VS Code container window. In a host terminal, list the dev containers and find the row whose checkout path matches your repository folder.

```text
docker ps -a --filter label=devcontainer.local_folder --format "{{.ID}}  {{.Image}}  {{.Labels}}"
```

Replace `CONTAINER_ID` with the ID from that row. Use `docker inspect` to find the named volume whose mount destination is `/opt/daqforge-venv`. Record that volume name and the image tag from the listing before removing the container.

```text
docker inspect CONTAINER_ID
```

Run the following commands in order, substituting the recorded values for the placeholders.

```text
docker stop CONTAINER_ID
docker rm CONTAINER_ID
docker volume rm VOLUME_NAME
docker image rm IMAGE_TAG
```

If the container was already removed, use `docker volume ls --filter name=daqforge-venv-` and `docker image ls --filter label=io.daqforge.devcontainer=true` to find leftover DAQForge resources. Remove the exact names or image tags for this checkout. Leave shared resources such as the `vscode` volume and the Python base image in place. Docker will keep images that other containers still use.

These commands leave the source files and `.git` on your host. Any generated files under the checkout, such as `dist/`, also stay there.
