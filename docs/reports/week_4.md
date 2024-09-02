# Week 4
June 17 - June 22

## which_r version R version choice validation

While working on the set_build_r issue, I initially overlooked the importance of testing and validating R version choices in the which_r script. I later rectified this by implementing a choice validation mechanism within the which_r script, ensuring users can seamlessly switch between different R versions in the R Dev Container. Special thanks to my mentor, Iain Emsley, for his valuable assistance in testing and refining this addition to the project.


**Issue Number** : [#127](https://github.com/r-devel/r-dev-env/issues/127)

**Pull Request** : [#136](https://github.com/r-devel/r-dev-env/pull/136)


**which_r helper script**

The which_r script is designed to help users switch between different R versions available in the R Dev Container. It allows the user to select the R version they want to use in new R terminals. This script lists all the available R builds in the container and updates the VS Code settings to point to the selected R version.

```sh
#!/bin/bash

# Specify the parent directory
parent_dir="/workspaces/r-dev-env/build"  # need to change to dynamic afterwards //TODO

# Path to the settings.json file
settings_file_path="/home/vscode/.vscode-remote/data/Machine/settings.json"

# Ask user which R version to use
echo "Which version of R should be used in new R terminals?"
echo "  1. R 4.4.0 (release version built into this container)"

# Check for additional R versions in subdirectories
if [ -d "$parent_dir" ]; then
  # Create an array to store subdirectory names
  subdirs=()
  
  # Loop through subdirectories and print numbered list
  counter=2  # Start counter at 2 to avoid conflict with built-in R
  for dir in "$parent_dir"/*; do
    if [ -d "$dir/bin" ] && [ -x "$dir/bin/R" ]; then
      subdir=$(basename "$dir")
      subdirs+=("$subdir")  # Populate subdirs array
      echo "  $counter. $subdir"
      ((counter++))
    fi
  done
  
  # If no additional R builds were found
  if [ ${#subdirs[@]} -eq 0 ]; then
    echo "No additional R builds available."
  fi
fi

# If no additional R builds were found
if [ ${#subdirs[@]} -eq 0 ]; then
  range=1
  echo "No additional R builds available."
else
  range=$((counter - 1))
fi

# Get user choice
read -p "Enter the number corresponding to the selected version: " choice

# Define selected version based on choice
if [[ "$choice" -eq 1 ]]; then
  # Use built-in R
  selected_version="/usr/bin/R"
elif [[ "$choice" -ge 2 ]] && [[ "$choice" -lt "$counter" ]]; then
  # Use R from chosen subdirectory
  chosen_subdir="${subdirs[((choice - 2))]}"  
  selected_version="$parent_dir/$chosen_subdir/bin/R"
else
  # Invalid choice, default to built-in R
  if [[ $range -eq 1 ]]; then
    echo "Invalid choice, please enter 1. Defaulting to built-in R version."
  else
    echo "Invalid choice, please select options between 1 to $range. Defaulting to built-in R version."
  fi
  selected_version="/usr/bin/R"
fi

# Update settings.json with the chosen R path
updated_settings_data=$(cat "$settings_file_path" | jq --arg subdir "$selected_version" '."r.rterm.linux"=$subdir')
echo "$updated_settings_data" > "$settings_file_path"

echo "R terminal will now use version: $selected_version"
```

**Explanation:**

- Directory and File Paths: The script specifies a directory where R builds are stored and the path to the VS Code settings file that will be updated.
- User Prompt: It prompts the user to select which R version to use, listing the built-in R version first.
- Checking for R Builds: The script searches the specified directory for additional R builds and lists them as options if found.
- User Input Handling: Based on the user’s choice, the script sets the selected R version. If an invalid choice is made, it defaults to the built-in R version.
- Updating VS Code: The script updates the VS Code settings file with the selected R version so that new terminals use the correct version.
- Confirmation: It then confirms which R version will be used in new R terminals.

## Use function vs script for set_build_r

In this issue, the set_build_R script was encountering permission issues, which prevented it from functioning properly. To resolve this, the script was added to .bashrc, and it was restructured into a function to make it more readable and maintainable.

**Issue Number** : [#133](https://github.com/r-devel/r-dev-env/issues/133)

**Pull Request** : [#137](https://github.com/r-devel/r-dev-env/pull/137)



**Learnings:**
I realized the importance of thoroughly testing each pull request before moving on to the next issue. This experience taught me the value of creating test cases for every possible scenario to ensure robust solutions.