# Week 6
July 1 - July 6

## Update to Docker Build Actions

The actions weren't running when changes were made to the VERSION file, so I updated the workflow to include the VERSION file as a trigger. Now, the actions will run automatically whenever any changes are made to this file.

**Pull Request** : [#151](https://github.com/r-devel/r-dev-env/pull/151)

-----------------


## Troubleshoot docs for rsync

Due to the "Rsync server Connection Limit Reached" error, the R build process would occasionally halt. To address this, I added information about the issue in the documentation to help others understand and troubleshoot the problem if they encounter it.

**Issue Number** : [#139](https://github.com/r-devel/r-dev-env/issues/139)

**Pull Request** : [#152](https://github.com/r-devel/r-dev-env/pull/152)


------------------


## Valgrind Installation for C lang support

R is built upon C, C++, and Fortran, so we initially considered installing Valgrind in the Docker image to assist with debugging and memory analysis. However, we discovered that Valgrind was already included in the Docker image. This discovery was beneficial, as it saved us the effort of installing and configuring it separately. Consequently, we decided to close the pull request without merging it.
Instead, we added information about Valgrind’s availability to the documentation to ensure users are aware of this tool's presence and functionality within the Docker environment.

**Issue Number** : [#101](https://github.com/r-devel/r-dev-env/issues/101)

**Pull Request** : [#154](https://github.com/r-devel/r-dev-env/pull/154), [#163](https://github.com/r-devel/r-dev-env/pull/163)(24/July)