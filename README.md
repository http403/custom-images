# Custom Images

A mono repo for all my customized container images needs with (attempted) automatic updates.

This is my first time of building a monorepo with a CI/CD so things can break from time to time.

## Versioning/Tagging
> **WARN**: There will not be a tag nor release on GH as the update process doesn't change the code. The version/tag here refers to the resulting images.

Since a container can involves multiple components, and each components can have a different scheme of versioning/progress. The version/tag of the container images will be date coded instead, on the day it is build. By default, the update should be done once a day so conflicting tags shouldn't be a problem. Even if there is a conflict, the newer version shall overwrite the older version in the same day. Not ideal but should be accpetable. 

## How this works

### Auto Update
Since there won't be releases (see [Versioning/Tagging](#versioningtagging)), and there is a need to keep track of componets versions being used in each image, I hacked up a solution that's a bit convoluted that uses issues.

1. A workflow on schedule that will check for updates
    - It first list out all the containers and filter the one have `update.py` in it.
    - It then use matrices to create a job for each container and runs the `update.py` script.
2. When a update is detected in the script, a new issue with the container name (i.e. the directory name), and `ci-build` tagged.
    - The script look in the closed issues with the correct title and tag
    - Extract the componets version information from the last issue (the biggest issue number) in the matching set
3. Another workflow with looks for all open issues 
    - It first filters for those have `ci-build` label but excluding `ci-failed`
    - Then it uses the Dockerfile within to build the container

### New Container
1. Anyone make a PR containing exactly one container defination and tag with `new-container`
2. The build workflow will pick up and make a test build, pass then merge which will be published to the packages

