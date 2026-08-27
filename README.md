# Flatpak QGIS packaging

Just flatpak packaging for QGIS.

## I need more python modules!
Often you will need extra python modules to run extra plugins. We can't possibly package them all with
the application. To get them in your system, you can install them locally as follows:

```
flatpak run --devel --command=pip3 org.qgis.qgis install scipy --user
```

Where `scipy` is the package you want, replace it with whatever you think it might be necessary.

If you feel like it definitely should be bundled, don't hesitate to report an issue in this repository.

## How to build flatpak image locally

### TL;DR
```
git clone --recurse-submodules ...
flatpak-builder --force-clean --disable-updates --ccache --user --sandbox --install ./build org.qgis.qgis.json
```

### Description
When cloning you can add `--recurse-submodules` flag to pull all submodules otherwise you can run: `git submodule update --init --recursive` after clone.

To build flatpak image you can use flatpak-builder tool.

To install flatpak-builder you can try (on Ubuntu/Debian based systems):
```
sudo apt install flatpak-builder
```

Build and install the image with this command:
```
flatpak-builder --disable-updates --ccache --user --sandbox --install ./build org.qgis.qgis.json
```
if you're building again you can add: `--force-clean` which will clear `./build` dir.

Or with flatpak version of flatpak builder:
```
flatpak run org.flatpak.Builder --disable-updates --ccache --user --sandbox --install --force-clean ./build org.qgis.qgis.json
```

Alternatively you can try commands executed by the CI machine, which are more or less:
```
flatpak run org.flatpak.Builder -v --force-clean --sandbox --delete-build-dirs --user --install-deps-from=flathub --install-deps-from=flathub-beta --arch x86_64 --bundle-sources --extra-sources=./downloads --default-branch test ./build org.qgis.qgis.json --download-only
flatpak run org.flatpak.Builder -v --force-clean --sandbox --delete-build-dirs --user --install-deps-from=flathub --install-deps-from=flathub-beta --arch x86_64 --bundle-sources --extra-sources=./downloads --default-branch test ./build org.qgis.qgis.json --disable-download --install
```

### Build errors
If you get:
```
error: org.kde.Sdk/x86_64/5.15-23.08 not installed
Failed to init: Unable to find sdk org.kde.Sdk version 5.15-23.08
```
then run flatpak-builder with `--install-deps-from=flathub` flag or install missing SDKs by running commands like:
```
flatpak install flathub org.kde.Sdk/x86_64/5.15-23.08
```

If you get an error about file transfer protocol not being allowed for git submodule then see: https://github.com/flatpak/flatpak-builder/issues/495

If you get errors about being unable to locate OpenGL includes then see: https://github.com/flathub/org.qgis.qgis/issues/26
Probably you need to init the submodule and update it to make it actually pull it.

If you get error:
```
Downloading https://mesa.freedesktop.org/archive/glu/glu-9.0.1.tar.xz
100   162  100   162    0     0    309      0 --:--:-- --:--:-- --:--:--   310
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
Failed to download sources: module glu: server certificate verification failed. CAfile: none CRLfile: none
```
then see: https://github.com/flatpak/flatpak-builder/issues/468 or https://github.com/flathub/shared-modules/issues/230

### Updating arrow version
Change tag and commit for arrow in `org.qgis.qgis.json` to version you want to update to.

Check `cpp/thirdparty/versions.txt` ( [e.g. version 17.0.0](https://github.com/apache/arrow/blob/apache-arrow-17.0.0/cpp/thirdparty/versions.txt) ) file in arrow repo for that version and compare versions of dependencies.
For each dependency you need to update the url, the downloaded file name, and file name exported to env variable.
