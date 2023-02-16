# Conan Cross-Compile

A project to build packages for 3rd party libraries and upload to personal Artifactory repo. This is mostly exploratory to learn Github actions and how to run actions in a smart way that doesn't burn Action minutes unnecessarily. 

I make use of the Conan Search JSON output feature which is still experimental as of v1.58 so I pin Conan to that version. Updating Conan will require inspection of the JSON search results to ensure the format has changed in a way that breacks searches.

I will also use the built packages for personal side projects on the Verdin iMX8M+ board and Raspberry Pi boards.

## Target Platforms

- Linux x86_64, ARMv8, and Android
- Mac x86_64 and ARMv8
- Windows x86_64

## Secrets

This repo requires a Conan user name and password to push libraries up to Conan. I am using the [organizational secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) feature in GitHub for this. 

The stored secrets are:

- CONAN_USERNAME
- CONAN_PASSWORD

## Cross compiler

I install the following cross compiler for Linux ARMv8 platform:

```
apt install gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu
```

The install folder on Ubuntu will be /usr/aarch64-linux-gnu

# How to Add Libraries

## Create Recipe

Typically it is adaquate to search for the desired package in Conan-Center. If it exists, it will typically only be built for Windows which is the reason we have this repo to cross compile for other platforms. Copy the conanfile.py and conandata.yml files to a folder with the following path:

```
recipes/<pkg name>/<pkg version>
```

It is likely that changes are not required but there is no guarantee.

## Add the library meatadata to the appropriate build yaml file.

Each platform has a yaml file titled "build-libraries-for...". Libraries to be built are listed in alphabetical order and have the following form:

```
- name: Build <pkg name>
  id: <pkg name>-<pkg version>
  uses: ./.github/actions/conan-create
  with:
    package_name: <pkg name>
    package_version: <pkg version>
    profile_build: conan-profiles/<conan profile build>
    profile_host: conan-profiles/<conan profile host>
    recipe_directory: 'recipes/<pkg name>/<pkg version>'
```

# Commands to inspect Conan Search results

### Should return two valid packages
conan search -r peak8 "zlib/1.2.13@peak8/production" -q "os=Linux AND arch=armv8" --json search.json

### Should return error with empty results array
conan search -r peak8 "zlib/1.2.11@peak8/production" -q "os=Linux AND arch=armv8" --json search.json

### Should return valid results but with empty packages array
conan search -r peak8 "zlib/1.2.13@peak8/production" -q "os=Linux AND arch=armv7" --json search.json

# Commands to inspect built packages

### Linux x86_64 - Debug
file ~/.conan/data/zlib/1.2.13/peak8/production/package/0508f825aee0fe3099a5dae626a5316104c6db0a/lib/libz.so

### Linux x86_64 - Release
file ~/.conan/data/zlib/1.2.13/peak8/production/package/1748639999ed79b998e4fe4a6d292ed8e874736a/lib/libz.so

### Linux ARMv8 - Debug
file ~/.conan/data/zlib/1.2.13/peak8/production/package/bef5299822e42e96f0e878760beb2bf99ad4cd55/lib/libz.so

### Linux ARMv8 - Release
file ~/.conan/data/zlib/1.2.13/peak8/production/package/115c8fa606e08a868a5ec8073c26c1a096c7263b/lib/libz.so

### Macos x86_64 - Debug
file ~/.conan/data/zlib/1.2.13/peak8/production/package/d98fae1010d1fb9e7f79a1e8a72bbf129d8660a2/lib/libz.dylib

### Macos x86_64 - Release
file ~/.conan/data/zlib/1.2.13/peak8/production/package/647afeb69d3b0a2d3d316e80b24d38c714cc6900/lib/libz.dylib

### Macos ARMv8 - Debug
file ~/.conan/data/zlib/1.2.13/peak8/production/package/baf78352c82543f997ec32332829b6fa30714eaa/lib/libz.dylib

### Macos ARMv8 - Release
file ~/.conan/data/zlib/1.2.13/peak8/production/package/8fafab5ebfba468fd21a497cfee65cafe294bd9e/lib/libz.dylib

### Windows x86_64 - Debug
file ~/.conan/data/zlib/1.2.13/peak8/production/package/8cf01e2f50fcd6b63525e70584df0326550364e1/bin/zlibd.dll

### Windows x86_64 - Release
file ~/.conan/data/zlib/1.2.13/peak8/production/package/6cc50b139b9c3d27b3e9042d5f5372d327b3a9f7/bin/zlib.dll