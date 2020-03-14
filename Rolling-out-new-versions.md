This assumes you have a clean clone of the Github repository.

The process is as follows:
1. Prepare the develop branch for release by merging master
```bash
git checkout develop
git merge master
```
2. Update the versions where they need to be. This can include
    - The CMake version numbers in `./CMakeLists.txt`
    - The LV2 version number in `./cmake/LV2Config.cmake` if there was changes to the LV2 ports and addition/removal of options that affect the ABI of the plugin (not the library!)
    - The VST version number if there was changes to the internal VST state format
3. Merge develop in master
```bash
git checkout master
git merge --no-ff develop
git tag -a x.y.z
```
4. Push and let CI run
5. In case of deployment problems, correct things that need to be. If there are new changes, remerge master in develop
```bash
git checkout develop
git merge --no-ff master
```