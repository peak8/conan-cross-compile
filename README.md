# Conan Cross-Compile

A simple project to explore cross-compiling using Conan and Github Actions. I am starting with building a 3rd party library and deploying to a personal artifactory account. This is kickstarted by examining the project Justin set up for Solstice and building on it. Then I will make a simple hello world app targetting the Verdin iMX8M+ module. Here I will have to learn creating a custom recipe.

## Target Platforms

This repo will build Debug and Release versions for Linux x86 64-bit systems and ARMv8 64-bit systems such as the Verdin iMX8M+ board.

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









# main.yml - an experiment for manifests
# This is the main workflow for the conan-cross-compile project. At this level we launch workflows based on
# target platform or project. 

name: Main Workflow

on:
  push:
    branches: 
      - 'main'
      - 'alternative-structure'
  pull_request:
    branches: [ main ]
  workflow_dispatch:

env:
  CONAN_USERNAME: ${{ secrets.CONAN_USERNAME }}
  CONAN_PASSWORD: ${{ secrets.CONAN_PASSWORD }}
  CONAN_NON_INTERACTIVE: 1
  CONAN_REVISIONS_ENABLED: 1
  CONAN_LOGGING_LEVEL: debug
    

jobs:
  what-to-build:
    runs-on: ubuntu-22.04
    strategy:
      matrix:
        manifest: 
          - linux-x86_64.json
    # outputs:
    #   result: ${{ steps.?.outputs.? }}
    steps:
      - uses: actions/checkout@v3.3.0

      - name: Install Conan
        run: pip install conan

      - name: Configure Conan
        uses: ./.github/actions/configure-conan

      - name: Install JSON Parser
        run: sudo apt install jq

      - name: Download all Conan package metadata
        id: download-all-conan-package-metadata
        shell: bash
        run: conan search -r peak8 --json search-conan-all.json
      
      - name: Read manifest
        id: read-manifest
        shell: bash
        run: |
          export MANIFEST=$(jq .libraries[0].package_name manifests/${{ matrix.manifest }})
          echo "manifest=${MANIFEST}" >> $GITHUB_OUTPUT
      - name: Read manifest length
        id: read-manifest-length
        shell: bash
        run: |
          export LENGTH=$(jq '.libraries | length' manifests/${{ matrix.manifest }})
          echo "length=${LENGTH}" >> $GITHUB_OUTPUT
      - name: For loop
        id: for-loop
        shell: bash
        run: |
          for i in length
          do
            export MANIFEST=$(jq .libraries[$i].package_name manifests/${{ matrix.manifest }})
            echo "manifest=${MANIFEST}" >> $GITHUB_OUTPUT
          done