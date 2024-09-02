# Week 11
August 5 - August 10


## Using prebuild docker image in gitpod.yml and Container Version issue

I specified a Docker image tag instead of allowing Gitpod to build the workspace using the .gitpod.Dockerfile. This adjustment resolved the issue of the container version not displaying correctly in the R Dev Container within Gitpod.


**Pull Request** : [#168](https://github.com/r-devel/r-dev-env/pull/168)

```Dockerfile
image:
  file: .gitpod.Dockerfile <- previous code
  #after first build of gitpod docker image change file to image with the tag.
image: ghcr.io/r-devel/r-dev-env:gitpod.devel
```

## Optimize Docs Assets
To optimize the documentation, we came up with to optimize docs images/assets and to do so I used R lang to optimize images. And to automate this process I used actions workflow.
Whenever a new image is added to assets directory the workflow runs and optimize the specific image(which are not optimized).


**Issue Number** : [#161](https://github.com/r-devel/r-dev-env/issues/161)

**Pull Request** : [#172](https://github.com/r-devel/r-dev-env/pull/172)

```R
library(magick)
library(tools)

# Define the directory containing images
image_dir <- file.path("docs", "assets")

# Get all photos in the directory with specified extensions
files <- dir(image_dir, pattern = "\\.(jpeg|jpg|png)$", ignore.case = TRUE)

# Process each file
for (i in seq_along(files)) {
    # Read the image
    file_path <- file.path(image_dir, files[i])
    img <- tryCatch({
        image_read(file_path)
    }, error = function(e) {
        message("Error reading image: ", file_path)
        next
    })

    # Resize the image to 800 px width using Triangle filter for resampling
    img <- image_resize(img, geometry_size_pixels(width = 800), filter = "Triangle")


    # Define the output path (overwrite original image)
    output_path <- file_path

    # Write the optimized image (overwrite original image)
    tryCatch({
        image_write(img, output_path, format = "jpg", quality = 82)
    }, error = function(e) {
        message("Error writing image: ", output_path)
    })
}
```

**Explaination:**
This R script processes images in the docs/assets directory by:

- Loading Required Libraries: Uses magick for image processing.
- Listing Image Files: Finds all .jpeg, .jpg, and .png files.
- Processing Each Image:
- Read: Loads each image.
- Resize: Adjusts the width to 800 pixels using the Triangle filter.
- Save: Overwrites the original file with a resized JPEG version, quality set to 82.
- Error Handling: Includes error handling for reading and writing images.

The goal is to resize and optimize images for better performance.


## LatinR Talk Abstract

In addition to working on the project, I was given the opportunity to present at the LatinR conference, scheduled for November. This being my first time submitting an abstract to a major R Project community event, I sought guidance from my mentors on crafting an effective abstract. Their advice was invaluable in preparing for this significant occasion.