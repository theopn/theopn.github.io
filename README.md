# Theo Website

My website(https://theopark.me/) ([or](https://theopn.github.io)) built with Hugo!

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

## Structure

File/directory with `(*)` mark are somethings you (meaning I) should know.

```
.
├── CNAME
├── README.md
├── archetypes                                      (*)
│   ├── default.md
│   └── writing.md
├── config.toml                                     (*)
├── content                                         (*)
│   ├── _index.md
│   ├── about.md
│   ├── projects.md
│   └── writing
│       ├── 20xx-xx-xx-blah-blah-0.md
│       └── 20yy-yy-yy-blah-blah-1.md
├── resources
│   └── idk what this is
├── static                                          (*)
│   ├── favicon.png
│   └── img
│       ├── about
│       │   ├── resume.pdf
│       │   └── portrait.jpg
│       ├── index
│       │   └── epic-image.jpg
│       ├── projects
│       │   ├── 20xx-xx-xx-epic-image.jpg
│       │   └── 20yy-yy-yy-epic-image-2.png
│       └── writing
│           ├── 20xx-xx-xx-funny-meme.jpg
│           └── 20yy-yy-yy-another-funny-meme.jpg
└── themes                                          (*)
    └── terminal
        └── Git submodule from terminal theme
```

### archetypes

//TODO

To initialize a new content under `content/<archetypes>`, use the following command:

```sh
hugo new <archetypes>/<name>
```

### config.toml

//TODO

### content

//TODO

### static

- `favicon.png`: Needs to be specified in the `config.toml` //TODO
- `img`: arbitrary organization for my sanity

### themes

I use [hugo-theme-terminal](https://github.com/panr/hugo-theme-terminal). The repository README contains a great documentation.

