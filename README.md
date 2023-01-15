# Conan Cross-Compile

A simple project to practice cross-compiling using Conan and Github Actions. I'll start with a simple hello world app and then add a library.

## Secrets

This repo requires a Conan user name and password to push libraries up to Conan. I am using the [organizational secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) feature in GitHub for this. 

The stored secrets are:

- CONAN_USERNAME
- CONAN_PASSWORD