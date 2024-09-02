# Week 8
July 15 - July 20

## Setting Up Gitpod Env for R Dev Container and Standardizing Helper scripts to work with both environments(codespaces and gitpod)
The work for R Dev Container version 0.3v starts from here. During this I worked around setting up streamlining environment for gitpod. And also did some refractoring of helper script to work with gitpod seemlessly.
I firstly added gitpod config using .gitpod.yml and .gitpod.Dockerfile for gitpod workspace.
Here we used official gitpod workspace base image this was to have a stable environment and to have a good support with gitpod.

**Issue Number** : [#153](https://github.com/r-devel/r-dev-env/issues/153), [#111](https://github.com/r-devel/r-dev-env/issues/111), [#123](https://github.com/r-devel/r-dev-env/issues/123), [#92](https://github.com/r-devel/r-dev-env/issues/92)

**Pull Request** : [#158](https://github.com/r-devel/r-dev-env/pull/158)


**.gitpod.Dockerfile**

```Dockerfile
FROM gitpod/workspace-full:latest
```
Other things were almost similar to that of the codespace environment.

**.gitpod.yml**

```yml
image:
  file: .gitpod.Dockerfile
  #after first build of gitpod docker image change file to image with the tag.

tasks:
    - name: Initial Setup for R-dev-env Gitpod Workspace
      before: |
         bash /workspace/r-dev-env/scripts/localscript.sh
         source ~/.bashrc
vscode:
    extensions:
     - REditorSupport.r
     - johnstoncode.svn-scm
```


This week, I focused on configuring the gitpod.yml file to define and customize the development environment for Gitpod workspaces. I executed a local script that sets up the initial environment for the R Dev Container and added useful VSCode extensions, including R Editor and SVN. Additionally, I updated the helper scripts and transferred environment path variables from the Dockerfile to these scripts to streamline the setup process.

**localscript file**

```sh
local_script(){

# Workspace root is $PWD at the start
WORK_DIR=$PWD
VSCODE_DIR="$WORK_DIR/.vscode"
DEVCONTAINER_JSON="$WORK_DIR/.devcontainer/devcontainer.json"

# Create patch directory in workspace root ($PWD at start)
PATCHDIR="$WORK_DIR/patches"
```

I updated the local script to dynamically retrieve the present working directory (PWD) and use it as the WORK_DIR. This directory will serve as the base path for the helper scripts, ensuring that they function correctly and perform their tasks effectively.

--------------------------------

## Workflow to Build Gitpod Workspace Docker Image

Here I added a docker build workflow to build gitpod docker image.


**Issue Number** :[#160](https://github.com/r-devel/r-dev-env/issues/160)

**Pull Request** : [#162](https://github.com/r-devel/r-dev-env/pull/162)


```yml
- name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .gitpod.Dockerfile
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:gitpod.${{ github.ref_name }}
          build-args: |
            CONTAINER_VERSION=${{ env.VERSION }}
          labels: ${{ steps.meta.outputs.labels }}
```