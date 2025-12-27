+++
date = '2025-12-27T12:25:26-05:00'
draft = false
title = 'I spent $30 on the monospaced Comic Sans'
tags = ["Linux", "macos"]
+++

I have been a subscriber of [Wolfgang's Channel](https://www.youtube.com/@WolfgangsChannel) since he was a real alpha male using ThinkPad T440p with Gentoo, long before he became a [soyboy latte sipping gender studies college kid](https://www.youtube.com/watch?v=X0DIHlnD_S0) using his filthy Apple Silicon MacBook.

YouTube recently recommended his old video: [Why I Use a Paid Terminal Font](https://www.youtube.com/watch?v=1zvWXT53puQ).
Wait a minute, it gets even worse, Comic Code is not only a paid terminal font, but as the name suggests, it tries to be the MONOSPACED COMIC SANS!

Hear me out.
As terrible as it sounds, I would rather have a chuckle than a sigh when I look at my computer.
Also, even though other free projects like [Comic Shanns](https://github.com/shannpersand/comic-shanns) and [Comic Mono](https://dtinth.github.io/comic-mono-font/) exist, they are incomplete.
[Toshi Omagari](https://tosche.net/fonts), the designer of Comic Code, is an amazing typeface designer, and he completely redrew the font and ligatures.
(Comic Code "only takes inspiration from Comic Sans and was drawn entirely from scratch and legally distinct.")

I remembered the urge to buy the font when I first watched the video ~3~ 4 years ago, but I was a responsible college freshman who did not want to waste my parents' hard-earned money on a \$30 font.
Instead, I used [Fantasque Sans](https://github.com/belluzj/fantasque-sans) for a while, which is another Comic-Sans-esque, fun, monospaced font, but the itch never went away.
I also noticed that Wolfgang is still using the font to this day (7:24 in [his latest video](https://www.youtube.com/watch?v=J9dBOx1CD4w)).

4 years later, I am much more numb to irresponsible purchases, and I have decided that my terminal deserved some fun.

{{< figure src="/images/2025-12-27-comic-code/new-macos-sc.jpg" caption="Screenshot taken from my MacBook Air sitting at a local cafe sipping matcha"  >}}

The [\$30 "Coding Essentials"](https://fonts.ilovetypography.com/fonts/tabular-type-foundry/comic-code#collections) set was more than enough, and I love how fun and silly it looks.
Can't wait to show other people when I get back to school.

## How I patched the font

1. Download the [Nerd Font Patcher](https://github.com/ryanoasis/nerd-fonts?tab=readme-ov-file#font-patcher)
2. Install `fontforge` and `argparse` (both available in Homebrew)
3. Locate the original fonts in the directory inside the `FontPatcher` directory
    ```
    FontPatcher
    ├── bin
    │   └── ...
    ├── comic-code-og
    │   ├── ComicCode-Bold.otf
    │   ├── ComicCode-BoldItalic.otf
    │   ├── ComicCode-Italic.otf
    │   ├── ComicCode-Medium.otf
    │   ├── ComicCode-MediumItalic.otf
    │   ├── ComicCode-Regular.otf
    │   ├── ComicCodeLigatures-Bold.otf
    │   ├── ComicCodeLigatures-BoldItalic.otf
    │   ├── ComicCodeLigatures-Italic.otf
    │   ├── ComicCodeLigatures-Medium.otf
    │   ├── ComicCodeLigatures-MediumItalic.otf
    │   └── ComicCodeLigatures-Regular.otf
    ├── font-patcher
    ├── glyphnames.json
    ├── readme.md
    └── src
        └── ...
    ```
4. Run the following command in the `FontPatcher` directory (change the glob if you want to patch the entire set or only the non-ligature versions)
    ```sh
    for f in ./comic-code-og/ComicCodeLigatures-*; do fontforge -script font-patcher $f; done
    ```
    This will generate
    ```
    ComicCodeLigaturesNerdFont-Bold.otf
    ComicCodeLigaturesNerdFont-BoldItalic.otf
    ComicCodeLigaturesNerdFont-Italic.otf
    ComicCodeLigaturesNerdFont-Medium.otf
    ComicCodeLigaturesNerdFont-MediumItalic.otf
    ComicCodeLigaturesNerdFont-Regular.otf
    ```
5. If glyphs look off, experiment with the following flags before
    ```sh
    # --single-width-glyphs: "Mono" (glyphs are strictly the same size)
    # --variable-width-glyphs: "Propo" (glyphs are proportionally spaced and take up as much space as you want)
    # default is double width
    ```
6. Install the `otf` files using `fc-cache` in Linux or just double-clicking in macOS

