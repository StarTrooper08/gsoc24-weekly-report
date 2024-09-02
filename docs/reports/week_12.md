# Week 12
August 12 - August 17


## Local Setup 

This feedback was provided by R contributor Elio. While the local setup used to function correctly, it didn't allow for file creation inside the R Dev Container. To address this, I added the relevant permissions to the Dockerfile and updated the local setup to support and ensure the new helper script functions correctly.

**Issue Number** : [#157](https://github.com/r-devel/r-dev-env/issues/157)

**Pull Request** : [#174](https://github.com/r-devel/r-dev-env/pull/174)

Also made some changes in which_r script to fetch work_dir in localsetup properly

**which_R helper script**

```sh
  elif [ "$HOME" = '/home/vscode' ]; then
    # Check if it's running in a Codespace
    if [ -d "/home/vscode/.vscode-remote" ]; then
      JSON_FILE_PATH="/home/vscode/.vscode-remote/data/Machine/settings.json"
    else
      # Handle local devcontainer setup
      JSON_FILE_PATH="$WORK_DIR/.vscode/settings.json"
    fi
```

**Explaination**:
- Check Home Directory: If the HOME environment variable is set to /home/vscode, it indicates the script is running in a GitHub Codespace or similar environment.
- Verify Codespace: If the .vscode-remote directory exists, it sets the JSON_FILE_PATH to the Codespace-specific settings file location.
- Local Devcontainer: If the .vscode-remote directory does not exist, it assumes the script is running in a local development container and sets the JSON_FILE_PATH accordingly.

## Index page

Since restructuring was done in the documentation directories and subdirectories, it was necessary to update the index.md file to reflect these changes. This ensures that the documentation displays in the correct structure when Codespace or workspace starts.

**Issue Number** : [#166](https://github.com/r-devel/r-dev-env/issues/166)

**Pull Request** : [#175](https://github.com/r-devel/r-dev-env/pull/175)


## Optimize Docs Images
The workflow to optimize docs images had issues so made some changes in worklflow.

**Issue Number** : [#161](https://github.com/r-devel/r-dev-env/issues/161)

**Pull Request** : [#177](https://github.com/r-devel/r-dev-env/pull/177)


## Gitpod Live Share docs

After completing R Dev Container gitpod environment setup, we also added quick quide for gitpod live share option.

**Pull Request** : [#178](https://github.com/r-devel/r-dev-env/pull/178)