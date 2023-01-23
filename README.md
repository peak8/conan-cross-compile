# Conan Cross-Compile

A simple project to explore cross-compiling using Conan and Github Actions. I am starting with building a 3rd party library and deploying to a personal artifactory account. This is kickstarted by examining the project Justin set up for Solstice and improving on it. Then I will make a simple hello world app targetting the Verdin iMX8M+ module. Here I will have to learn creating a custom recipe.

## Secrets

This repo requires a Conan user name and password to push libraries up to Conan. I am using the [organizational secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) feature in GitHub for this. 

The stored secrets are:

- CONAN_USERNAME
- CONAN_PASSWORD