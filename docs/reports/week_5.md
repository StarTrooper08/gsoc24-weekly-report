# Week 5
June 24 - June 29


## Fix: Automatically Detecting and Printing the Default R Version in which_r Script

While implementing choice validation in the which_r script, I decided to enhance it further by dynamically detecting and printing the default R version within the R Dev Container. Previously, the script explicitly printed a hardcoded R version, but with this update, it now automatically retrieves the latest default R version.

**Issue Number** : [#134](https://github.com/r-devel/r-dev-env/issues/134) 

**Pull Request** : [#144](https://github.com/r-devel/r-dev-env/pull/144)

**update to which_r helper script**

```sh
built_in_r_version=$(R --version | grep "^R version" | awk '{print $3}')

echo "Which version of R should be used in new R terminals?"
echo "  1. R 4.4.0 (release version built into this container)" <- previous code
echo "  1. R $built_in_r_version (release version built into this container)"
```

**Explaination:**
- Built-in R Version Detection: The script uses the R --version command to dynamically extract the version of the built-in R installation. This is achieved by piping the output through grep and awk to capture just the version number.
- Dynamic Version Printing: The detected R version is stored in the built_in_r_version variable and then printed within the script's prompt. This change ensures that the script always displays the correct version of the built-in R, even if it changes in the future.

-------------------------


## actions: docker build schedule for main branch and devel branch

I also updated the Docker build schedule actions to automatically rebuild the Docker image twice a month. Since the latest version of R is typically released twice a week, this workflow ensures that the Docker image always includes the most up-to-date version of R.

**Pull Request** : [#142](https://github.com/r-devel/r-dev-env/pull/142)


## Docs for Live Share

Added documentation on using Live Share, as this feature is not available in R Studio. Having comprehensive docs on Live Share usage is a valuable addition for collaborative work within the R Dev Container.

**Issue Number** : [#135](https://github.com/r-devel/r-dev-env/issues/135)

**Pull Request** : [#138](https://github.com/r-devel/r-dev-env/pull/138)


## Adding project version number to Documentation navbar

The mkdocs material has option to show repo info in navbar, so I did config in mkdocs to show project release version in navbar.

**Issue Number** : [#130](https://github.com/r-devel/r-dev-env/issues/130)

**Pull Request** : [#141](https://github.com/r-devel/r-dev-env/pull/141)


## Release of R Dev Container v0.2

Documentation split into set of markdown files. These are used to create the documentation website https://contributor.r-project.org/r-dev-env/ and the user-focused pages can be accessed directly within the codespace, linked from an index that opens when the codespace first starts. 
New documentation pages/topics:

- Restarting and stopping the codespace
- Live Share
- Local Setup
- Updating Source Code
- Creating a patch file
- Multiple R Versions
- SVN Help
- Contributor Guide (for R Dev Container itself)
- Troubleshooting
- Building R documentation now instructs to build R with recommended packages as it only takes a few more minutes to set up and enables all the tests to be run with make check.
- Updated GitHub Actions, so that the container is rebuilt twice a month with updated software, including the latest version of R.
- Disconnected Codespace from r-dev-env GitHub repository, so changes are not tracked as changes to code for the container itself.
- Updated Codespace settings so that checkouts of the R sources are recognised as Subversion repositories, connecting to VS Code version control tab.
- Updated Codespace settings so that the R code linter is turned off (cannot define linter for base R!)
- Enabled httpgd graphics device to replace the default of plotting to PNG.
- Modified the default settings of $BUILDDIR and $TOP_SRCDIR to give a more logical setup for working with multiple R versions.
- Added which_r script to set the R version to use in R terminals, rather than using sudo make install to overwrite installed R release version.
- Added set_build_r bash function to facilitate custom settings of $BUILDDIR and $TOP_SRCDIR for working with multiple R versions.


**Pull Request** : [#146](https://github.com/r-devel/r-dev-env/pull/146)
