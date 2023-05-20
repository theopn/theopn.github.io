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
hugo -t terminal # If config.yml does not specify the "theme" variable
```

## Directory Structure

File/directory with `(*)` mark are somethings you (meaning I) should know.

```
.
├── .git
├── .github                                         (*)
├── CNAME                                           (*)
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
├── public
│   ├── <Hugo generated HTML files>
│   └── <Does not exist if GitHub action is used>
├── resources
│   └── <Hugo generated temp files>
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
        └── <Git submodule from terminal theme>
```

### .github and CNAME

Custom GitHub workflow //TODO

For having a custom domain, have the domain in `CNAME`.

### archetypes

Archetypes are templates for Hugo contents. When archetypes are not specified, `default.md` archetype is used. Otherwise, an appropriate archetype will be used. In the beginning of the archetype (and every documents generated from the archetype) there is "Front Matter". Hugo supports YAML (`---\n date: ...\n description: ...\n ---`), TOML (`+++\n date: ...\n description: ...\n +++`), and JSON ({\n "date": "...",\n "description": "..."\n }), and I use YAML.

To initialize a new content under `content/<archetypes>`, use the following command:

```sh
hugo new <archetypes>/<name>.md
```

### config.toml

Copy of the default configuration from [the theme repository](https://github.com/panr/hugo-theme-terminal#how-to-configure). 

### content

- `_index.md`: For the front-page, root directory request. If the content from `contentTypeName` folder exists (refer to `config.toml`), they will be appended after `_index.md` content. In my usage, there is no contents in `contentTypeName` (which is `content/posts` -- directory that does not exist for my setup), purely `_index.md` content will be displayed for the root directory
- `about.md`: "About Me" page content
- `projects.md`

`writing` is a folder with all my posts. When a new content is created using `hugo new writing/yyyy-mm-dd-title.md` command, writing archetype will be used.

#### Deleting/Renaming a content

The files are not tied to Hugo structure. If a post needs to be deleted or renamed, do so using a system file manager. There is no special command needed

#### Inserting an image to a post

- To insert an image, first copy the image in `static/img/writing` directory. Then, use `figure` or `image` accordingly:

```html
 <!-- Image simply displays the image -->
{{< image src="/img/writing/ollie-thinkcat.jpg" alt="Oliver looking at Thinkpad" position="center" style="border-radius: 8px;" >}}
 <!-- Figure can decorate the image-->
{{< figure src="/img/writing/ollie-thinkcat.jpg" alt="ThinkPad and Oliver" position="center" style="border-radius: 8px;" caption="I told you it's a cute picture" captionPosition="left" captionStyle="color: black;">}}
```

#### Referencing other posts

```html
{{< ref "/writing/yyyy-mm-dd-title.md" >}}
```

#### Creating a new content type

Hugo recognizes multiple markdown contents in a folder and group them. For example, everything in `writing` folders are displayed under writing section.

If I create a new content using `hugo new blah/hi.md`, default template ("post" template from the theme author) will be used, `content/blah/hi.md` will be created, and `http://localhost:1313/blah/hi/` will be accessible. To display it in the menu, modify `languages.en.menu` section in the config.toml, as following:

```toml
    [languages.en.menu]
      [[languages.en.menu.main]]
        identifier = "about"
        name = "About Me"
        url = "/about"
      [[languages.en.menu.main]]
        identifier = "projects"
        name = "Projects"
        url = "/projects"
      [[languages.en.menu.main]]
        identifier = "writing"
        name = "Writing"
        url = "/writing"
      [[languages.en.menu.main]]
        identifier = "blah"
        name = "Blah"
        url = "/blah"
```

When `http://localhost:1313/blah/` is accessed, the list view of all contents within the folder will be displayed.

**To create a standalone document like `about.md` or `projects.md`**, simply follow how they are created. Hugo recognizes a standalone markdown file in `content` directory as standalone document.

### static

- `favicon.png`: Needs to be specified in the `config.toml` //TODO
- `img`: arbitrary organization for my sanity

### themes

I use [hugo-theme-terminal](https://github.com/panr/hugo-theme-terminal). The repository README contains a great documentation. Within the directory, the repository is cloned as a submodule with a folder named `terminal`. Refer to the theme name as `terminal`.

