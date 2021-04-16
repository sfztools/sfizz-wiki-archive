This assumes you have a clean clone of the Github repository.

The process is as follows:
1. Update the versions where they need to be. This can include
    - The CMake version numbers in `./CMakeLists.txt`
    - The LV2 version number in `./cmake/LV2Config.cmake` if there was changes to the LV2 ports and addition/removal of options that affect the ABI of the plugin (not the library!)
    - The VST version number if there was changes to the internal VST state format
    - Check for missing or wrong `@since` version numbers in Doxygen comments
2. Merge develop in master and tag; use the version number as a tag message
```bash
git checkout master
git merge develop
git tag -a x.y.z
git push origin master
git push origin x.y.z
```
3. Let CI run
4. In case of deployment problems, correct things that need to be. If there are new changes, remerge master in develop
```bash
git checkout develop
git merge master
```
5. Update and commit the next release version on develop (see 2)
