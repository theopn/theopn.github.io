# Theo Website

This repository holds Hugo sources for my website.

[My website](https://theopark.me/) or (https://theopn.github.io) is hosted through GitHub.

## Usage

```sh
# Clone the repository and initialize submodules
git clone --recurse-submodules https://github.com/theopn/theopn.github.io.git

# Testing server locally
hugo server

# No longer relevant due to the use of GitHub action - building HTML/CSS contents in /public directory
hugo -D
hugo -t terminal # If config.toml does not specify the "theme" variable
```

