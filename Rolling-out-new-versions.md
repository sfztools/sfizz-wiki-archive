This assumes you have a clean clone of the Github repository.

The process is as follows:
1. Prepare the develop branch for release by merging master
```bash
git checkout develop
git merge master
```
2. Update the versions where they need to be. This can include
    - The CMake version numbers in `./CMakeLists.txt`
    - The version number in `./appveyor.yml`; change the first line to `version: x.y.z-{build}`
    - The LV2 version number in `./cmake/LV2Config.cmake` if there was changes to the LV2 ports and addition/removal of options that affect the ABI of the plugin (not the library!)
    - The VST version number if there was changes to the internal VST state format
4. Merge develop in master
```bash
git checkout master
git merge --no-ff develop
git tag -a x.y.z
```
5. Push and let CI run
6. In case of deployment problems, correct things that need to be.
7. Merge master in develop
```bash
git checkout develop
git merge --no-ff master
```
8. Update the version number/names where they need to be:
    - The version in `./appveyor.yml`; change the first line to `version: develop-{build}`