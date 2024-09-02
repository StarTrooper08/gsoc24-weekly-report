
# Week 1 
May 27 - June 1


## Using `httpgd` for R graphics


The first week of the Official Coding Period kicked off with configuring the httpgd package.

**Issue Number** : [#100](https://github.com/r-devel/r-dev-env/issues/100)

**Pull Request** : [#110](https://github.com/r-devel/r-dev-env/pull/110)

The httpgd package in R provides a modern graphics device that serves plots via an HTTP interface, making it ideal for viewing graphical output in a web browser or other HTTP clients. This package excels in interactive and remote development environments like Jupyter Notebooks, RStudio Server, and cloud-based IDEs, where seamless plot access is essential.

In the R Dev Container environment, where the default behavior involves generating and displaying plots as PNGs, httpgd addresses several limitations. The default PNG display size often lacks visual clarity, and each new plot overwrites the previous one, preventing users from navigating backward or forward through plots created in a session. By configuring httpgd in the R Dev Container, users gain enhanced functionality, including adjustable plot sizes, the ability to navigate between plots, and features like zooming, significantly improving the plotting experience in VSCode and similar environments.

To enable this feature in the R Dev Container project, I installed the httpgd package in the Dockerfile:

**Dockerfile**

```Dockerfile
Rscript -e "install.packages('httpgd', repos='https://cran.rstudio.com')"
```

**Explanation:**

- This line is added to your Dockerfile to install the httpgd package in R.
- install.packages('httpgd', repos='https://cran.rstudio.com') installs the httpgd package from the specified CRAN repository.
- By adding this command to your Dockerfile, you ensure that the httpgd package is installed whenever a new Docker image is built, making it available in your R Dev Container environment.


Next, I configured VSCode settings using the devcontainer.json file to utilize httpgd in the R Dev Environment.

**Devcontainer.json**

```json
"r.plot.useHttpgd": true
```

**Explanation:**

- The r.plot.useHttpgd setting is a Boolean option (true or false).
- By setting this to true, you instruct VSCode to use httpgd as the default plotting device in your R development environment, which provides enhanced graphical capabilities compared to the default PNG-based plotting.

Before configuring httpgd, we used an .Rprofile file to manage PNG plotting with the VSCode-R extension.

**.Rprofile**

```R

options(vsc.use_httpgd = FALSE,
        vsc.dev.args = list(width = 800, height = 600))
```

This commit [#ea02cc](https://github.com/r-devel/r-dev-env/commit/ea02ccd4934868409ac1139f91cc32eabc8436d9) was authored by my mentor, Heather Turner.

After installing and configuring httpgd, the previous VSCode-R extension configuration was commented out. The .Rprofile file was then updated to customize the R environment within the development container, specifically tailored for full use of the VSCode-R extension. You can see the related commit here.

**.Rprofile**

```R

.Library.site <- "/usr/local/lib/R/site-library"
# .libPaths() already includes site library for default R version.
.libPaths(c(.libPaths(), .Library.site))

```

**Explanation:**

- .Library.site <- "/usr/local/lib/R/site-library" sets the path to the site-wide R library.
- .libPaths(c(.libPaths(), .Library.site)) adds the site-wide library to the library search paths in R. This ensures that the installed packages, like httpgd, are easily accessible during your R sessions.




----------------------------------------------------------------

## Setting Build Variables to Default and Adding a Script for Advanced Users
After resolving the httpgd issue, I moved on to another task related to environment variables in the Dockerfile and helper scripts.

**Issue Number** : [#103](https://github.com/r-devel/r-dev-env/issues/103)

**Pull Request** : [#116](https://github.com/r-devel/r-dev-env/pull/116)

This task involved updating the $BUILDDIR and $TOP_SRCDIR environment variables to facilitate the use of helper scripts for setting the R console/terminal, which is built in the terminal, and using an environment variable to specify a sub-directory for building R from source.

In earlier releases of the R Dev Container, the $BUILDDIR and $TOP_SRCDIR paths were static, and contributors had to append subdirectories, such as `$BUILDDIR/$sub_directory`, while building R. This approach had two drawbacks. First, appending the subdirectory to $BUILDDIR and $TOP_SRCDIR was not mentioned in the official R Dev Guide, potentially causing confusion among contributors familiar with the guide. Second, switching between subdirectories was cumbersome. For instance, if you had built two R versions—r-devel and r-raw—switching between them required manually specifying `$BUILDDIR/r-devel` or `$TOP_SRCDIR/r-raw` each time, increasing the risk of breaking the build or introducing bugs.

To address these issues, we added a default subdirectory called r-devel to both environment variables (BUILDDIR and TOP_SRCDIR) and created a script to change r-devel to another label, such as r-raw, if a contributor wanted to build multiple R versions. This script changes the subdirectory in both $BUILDDIR and $TOP_SRCDIR simultaneously, solving the problem of subdirectory switching while aligning the R Dev Container documentation with the R Dev Guide, thereby reducing confusion for contributors.

**Environment Variables in Dockerfile**

```Dockerfile

ENV DEFAULT_LABEL="r-devel"
ENV BUILDDIR='/workspace/r-dev-env/build/${DEFAULT_LABEL}'
ENV TOP_SRCDIR='/workspace/r-dev-env/svn/${DEFAULT_LABEL}'
ENV PATCHDIR='/workspace/r-dev-env/patches'

```

**Explanation:**

- These lines define environment variables in your Dockerfile, which will be used within the R Dev Container.
- DEFAULT_LABEL="r-devel" sets a default label for your build directory.
- BUILDDIR='/workspace/r-dev-env/build/${DEFAULT_LABEL}' defines the build directory path, with the default label (r-devel) as part of the path. This allows for flexibility in switching between different R versions by simply changing the label.
- TOP_SRCDIR='/workspace/r-dev-env/svn/${DEFAULT_LABEL}' sets the source directory path, similarly including the label to support different R versions.
- PATCHDIR='/workspace/r-dev-env/patches' defines the directory path for patches, though it does not depend on the label.



**set_build_r Helper Script**

```sh

#!/bin/bash

# Default values for build and source directories
# BUILDDIR="/workspaces/r-dev-env/build/"
# TOP_SRCDIR="/workspaces/r-dev-env/svn/"
# DEFAULT_LABEL="r-devel"

# New environment variable
NEW_LABEL=${1:-$DEFAULT_LABEL}

# Replace "r-devel" with the new label in default values
NEW_BUILDDIR="${BUILDDIR}${NEW_LABEL}"
NEW_TOP_SRCDIR="${TOP_SRCDIR}${NEW_LABEL}"

# Print the set directories
echo "The build directory is set to: $NEW_BUILDDIR"
echo "The source directory is set to: $NEW_TOP_SRCDIR"

# Export the directories as environment variables
export NEW_BUILDDIR
export NEW_TOP_SRCDIR
```

**Explanation:**

- This is a Bash script designed to allow contributors to change the default label (r-devel) to something else, such as r-raw, for creating and managing multiple R builds.
- The script begins by accepting a new label as a parameter (NEW_LABEL=${1:-$DEFAULT_LABEL}). If no new label is provided, it defaults to r-devel.
- It then replaces the default r-devel label with the new label in the build (NEW_BUILDDIR) and source (NEW_TOP_SRCDIR) directory paths.
T- he script prints out the new paths for clarity and exports these paths as environment variables, so they can be used in the current session or by other scripts.

Additionally, I modified the execution permissions for the script [commit #164c22](https://github.com/r-devel/r-dev-env/pull/116/commits/164c223abd2efadf74883cba6a3530718eab2b1f).

After testing, I found that it wasn't working as expected, but this provided valuable learning opportunities.

**Learnings:** This week, with several ongoing issues, I focused on maintaining clear and readable code by adding comments in scripts. This approach helped keep the progress steady and minimized potential blockages.