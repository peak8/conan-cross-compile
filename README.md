# Conan Cross-Compile

A project to build packages for 3rd party libraries and upload to personal Artifactory repo. This is mostly exploratory to learn Github actions and how to run actions in a smart way that doesn't burn Action minutes unnecessarily. 

I make use of the Conan Search JSON output feature which is still experimental as of v1.58. However, this appears to have been introduced in v1.19 so may be stable enough.

I will also use the built packages for personal side projects on the Verdin iMX8M+ board and Raspberry Pi boards.

## Target Platforms

- Linux x86 64-bit systems
- ARMv8 64-bit systems
- Mac Intel-based 64-bit systems
- Mac ARMv8 64-bit systems

## Secrets

This repo requires a Conan user name and password to push libraries up to Conan. I am using the [organizational secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) feature in GitHub for this. 

The stored secrets are:

- CONAN_USERNAME
- CONAN_PASSWORD

## Cross compiler

Installed the following:

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
    profile: conan-profiles/<conan profile>
    recipe_directory: 'recipes/<pkg name>/<pkg version>'
    os: <os>
    arch: <architecture>
```


