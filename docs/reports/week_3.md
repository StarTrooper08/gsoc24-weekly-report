# Week 3
June 10 - June 15

## Follow-up on Documentation Updates

Week 3 started with addressing the remaining sub-issues related to updating the documentation and following up on reviews for the changes made in the previous weeks.


**Issue Number** : [#96](https://github.com/r-devel/r-dev-env/issues/96), [#97](https://github.com/r-devel/r-dev-env/issues/97), [#99](https://github.com/r-devel/r-dev-env/issues/99), [#105](https://github.com/r-devel/r-dev-env/issues/105),[#108](https://github.com/r-devel/r-dev-env/issues/108), [#108](https://github.com/r-devel/r-dev-env/issues/108), [#113](https://github.com/r-devel/r-dev-env/issues/113), [#114](https://github.com/r-devel/r-dev-env/issues/114), [#115](https://github.com/r-devel/r-dev-env/issues/115), [#117](https://github.com/r-devel/r-dev-env/issues/117)

**Pull Request** : [#118](https://github.com/r-devel/r-dev-env/pull/118)


------------------------------------


## Continuing Work on Setting Build Variables and Script Enhancements

The changes made in Week 1 for setting build variables were not functioning as expected. I worked on this issue again alongside the documentation tasks. Finally, the set_build_r script worked as intended. Additionally, the multi_r script was renamed to which_r with some modifications. This script allows users to switch the R Interactive console to the selected R version built in the R Dev Container.

**Issue Number** : [#103](https://github.com/r-devel/r-dev-env/issues/103), [#104](https://github.com/r-devel/r-dev-env/issues/104)

**Pull Request** : [#118](https://github.com/r-devel/r-dev-env/pull/116)

**set_build_r helper script**

```sh
#!/bin/bash

# Check if the label argument is provided
if [ -z "$1" ]; then
  echo "Usage: $0 <label>"
  echo "BUILDDIR is currently set to $BUILDDIR"
  echo "TOP_SRCDIR is currently set to $TOP_SRCDIR"
else
  # Assign the argument to a variable
  LABEL=$1

  # Update the environment variables
  export BUILDDIR="/workspaces/r-dev-env/build/$LABEL"
  export TOP_SRCDIR="/workspaces/r-dev-env/svn/$LABEL"

  # Print the new values
  echo "BUILDDIR is now set to $BUILDDIR"
  echo "TOP_SRCDIR is now set to $TOP_SRCDIR"
fi
```

**Explanation:**

- This script, set_build_r, is designed to update environment variables used for building R in the R Dev Container.
- If no argument is provided when running the script, it simply displays the current values of the BUILDDIR and TOP_SRCDIR environment variables.
- If a label is provided, it updates these environment variables to point to specific directories that correspond to the given label.
- The script ends by printing the updated values of these variables.



**which_r helper script**

```sh
#!/bin/bash

# Specify the parent directory
parent_dir="/workspaces/r-dev-env/build"  # Assuming this is the correct path

# Path to the settings.json file
settings_file_path="/home/vscode/.vscode-remote/data/Machine/settings.json"

# Ask user which R version to use
echo "Which version of R should be used in new R terminals?"
echo "  1. R 4.4.0 (release version built into this container)"

# Check for additional R versions in subdirectories
if [ -d "$parent_dir" ]; then
  echo "  Additional R builds available:"

  # Create an array to store subdirectory names
  subdirs=()

  # Loop through subdirectories and print numbered list
  counter=2  # Start counter at 2 to avoid conflict with built-in R
  for dir in "$parent_dir"/*; do
    if [ -d "$dir" ]; then
      subdir=$(basename "$dir")
      subdirs+=("$subdir")  # Populate subdirs array
      echo "    $counter. $subdir"
      ((counter++))
    fi
  done
fi

# Get user choice
read -p "Enter the number corresponding to the selected version: " choice

# Validate user choice
if [[ ! "$choice" =~ ^[1-9]$ ]]; then
  echo "Invalid choice. Please enter a number between 1 and $(($counter - 1))"
  exit 1
fi

# Define selected version based on choice
if [[ $choice -eq 1 ]]; then
  # Use built-in R
  selected_version="/usr/bin/R"
else
  # Use R from chosen subdirectory
  chosen_subdir="${subdirs[((choice - 2))]}"  
  selected_version="$parent_dir/$chosen_subdir/bin/R"  
fi

# Update settings.json with the chosen R path
updated_settings_data=$(cat "$settings_file_path" | jq --arg subdir "$selected_version" '."r.rterm.linux"=$subdir')
echo "$updated_settings_data" > "$settings_file_path"

echo "R terminal will now use version: $selected_version"
```

**Explanation:**

- The which_r script allows users to select which version of R they want to use in new R terminals within the development environment.
- The script starts by specifying a parent directory where different R builds are stored.
- It then prompts the user to select which version of R they want to use: either the built-in version or one of the additional builds available in subdirectories.
- The user's choice is validated, and the path to the selected R version is updated in the VS Code settings.json file. This ensures that new R terminals use the selected version.
- The script ends by printing the path to the selected R version.

------------------------


## Update to Docker Build workflow

The docker build workflow was updated to tag docker image with branch name dynamically.

**Pull Request** : [#131](https://github.com/r-devel/r-dev-env/pull/131)

**docker-build actions update**
```yml
 tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.ref_name }}
          build-args: |
            CONTAINER_VERSION=${{ env.VERSION }}
          labels: ${{ steps.meta.outputs.labels }}
```


**Explanation:**

- `tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.ref_name }}`: This line dynamically tags the Docker image with the branch name using github.ref_name. This is helpful for identifying which branch the image was built from.
- `build-args:`: The build-args section passes build arguments to the Docker build process. In this case, it sets CONTAINER_VERSION to the value of the VERSION environment variable.
- `labels:`: This line applies labels to the Docker image using the metadata generated by the Docker Metadata Action. Labels are useful for adding metadata to images, which can be helpful for tracking and automation.

**Learnings:**
This week, I misunderstood a review from my mentor regarding the set_build_var issue. Instead of temporarily saving the subdirectory as a label, I tried to save every label inputted by contributors into a file. However, I eventually made the necessary changes, and everything worked as expected. This experience taught me that communication is key—if I had clarified my doubts with my mentors earlier, the issue could have been resolved much sooner. As a professional software developer, over-communicating can sometimes be beneficial.