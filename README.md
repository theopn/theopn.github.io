# Theo Website

This repository holds Hugo sources for my website.

[My website](https://theopark.me/) or (https://theopn.github.io) is hosted through GitHub.

## Usage

```sh
# Clone the repository and initialize submodules
git clone --recurse-submodules https://github.com/theopn/theo-website.git
cd theo-website
git submodule update --remote

# After making changes
hugo -D
cd public
git checkout main
git add . # then commit push operation
```

