# Week 2
June 3 - June 8

Week 2 began with updating the documentation and github actions for building docker image.

## Updating the Documentation

The previous documentation had become outdated due to significant changes made during the transition from version 0.1 to 0.2. Additionally, a new Material UI design was introduced, and the images in the documentation were being loaded as external links, leading to slow loading times when the documentation webpage was opened. To address this, it was necessary to consolidate these images into a single asset folder under the docs directory.
And this issue was broken down into pieces(sub issues).

This task was broken down into several sub-issues. I worked on a handful of these issues, receiving valuable feedback from my mentors along the way.

The last commit I made to the documentation for this week can be found here[#ea88a9](https://github.com/r-devel/r-dev-env/pull/118/commits/ea88a90c3ef5da243195c1c4ce302d1f6bf19001).


**Issue Number** : [#96](https://github.com/r-devel/r-dev-env/issues/96), [#97](https://github.com/r-devel/r-dev-env/issues/97), [#99](https://github.com/r-devel/r-dev-env/issues/99), [#105](https://github.com/r-devel/r-dev-env/issues/105),[#108](https://github.com/r-devel/r-dev-env/issues/108), [#108](https://github.com/r-devel/r-dev-env/issues/108), [#113](https://github.com/r-devel/r-dev-env/issues/113), [#114](https://github.com/r-devel/r-dev-env/issues/114), [#115](https://github.com/r-devel/r-dev-env/issues/115), [#117](https://github.com/r-devel/r-dev-env/issues/117)

**Pull Request** : [#118](https://github.com/r-devel/r-dev-env/pull/118)


--------------------------


## Docker Build workflow

The docker build actions was building docker image using PR number as tag. So it was quickly fixed to used devel tag for docker image.

**Pull Request** : [#120](https://github.com/r-devel/r-dev-env/pull/120)

**docker-build actions update**

```yml
uses: docker/metadata-action@v5
  with:
    images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
    images: ${{ env.REGISTRY }}/${{ github.repository }}
    tags: |
      type=ref,event=branch
      type=ref,event=pr
jobs:
  with:
    context: .
    push: true
    build-args: |
      "CONTAINER_VERSION=${{ env.VERSION }}"
    tags: ${{ steps.meta.outputs.tags }}
    labels: ${{ steps.meta.outputs.labels }}
    tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:devel

```
**Explaination:**

This Docker build action workflow is designed to automate the process of building, tagging, and pushing Docker images. Initially, the images were tagged based on the pull request number, but the workflow was updated to consistently use the devel tag for development builds. This change makes it easier to identify and manage development images in the Docker registry.


**Learnings:**
While working on the documentation updates, I made the mistake of combining several sub-issues into a single pull request (PR). Although this initially seemed like an efficient approach, it made the review process more complicated as more changes were added. This created a bottleneck for both the maintainers and myself, slowing down progress.

Typically, I create one PR per issue, but since these sub-issues were part of a larger documentation update, I opted to keep them in one PR. However, this approach backfired. Moving forward, I've learned the importance of creating PRs that are easy to review. It’s also a good open-source practice to have one PR per issue and to make changes in manageable chunks.
