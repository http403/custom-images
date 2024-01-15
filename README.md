# Custom Images

A mono repo for all my customized container images needs with (attempted) automatic updates.

This is my first time of building a monorepo with a CI/CD so things can break from time to time.

## Versioning/Tagging
> **WARN**: There will not be a tag nor release on GH. The version/tag here refers to the resulting images.

Since a container can involves multiple components, and each components can have a different scheme of versioning/progress. The version/tag of the container images will be date coded instead, on the day it is build. By default, the update should be performed daily so tag conflict shouldn't be a problem. Even if there is a conflict, the newer version shall overwrite the older version in the same day. Not ideal but should be accpetable. 

## How this works

### Auto Update
1. A workflow on schedule that will check for updates
    - It first list out all the containers and filter the one have `update.py` in it.
    - It then use matrices to create a job for each container and runs the `update.py` script.
2. The script will run through the checks, and if there is an update, it will update the `version.yml` within the directory, and open a pull request.
3. Another workflow (`build.yml`) will triggered by the pull request.
    - `build.py` will be invoked and build the image
4. If success, auto merge and close the PR, and trigger `build.yml` again.
    - `build.py` will be invoked first the have the image ready
    - then `deploy.py` will be invoked and pushes the image to defined registries.

### New Container
1. Make a PR that modify the `containers` directory. Each PR can contains more than one container definitions but recommend to keep at one.
2. The build workflow will pick up and the rest is similar to how auto update does starting at step 3. However, it won't auto merge. It must be approved manually.
