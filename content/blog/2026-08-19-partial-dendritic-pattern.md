+++
date = '2026-08-19T11:06:00-04:00'
draft = false
title = 'Partial(?) Dendritic Pattern for My NixOS Config'
tags = ["NixOS", "Nix", "Linux"]
+++

> See my [Nix Config](https://github.com/theopn/nix-conf) repository for more details.

I migrated to [NixOS](https://nixos.org/) (and [nix-darwin](https://github.com/nix-darwin/nix-darwin)) about 6 months ago, and it quickly became *the* operating system for me.
Even before discovering Nix, I meticulously organized my Dotfiles and did regular system cleanups, operating on the philosophy: "if it's not in my dotfiles repo, it doesn't exist on my system."

Looking back, I wonder why it took me so long to adopt NixOS.
It achieves exactly what I want, which is a system entirely defined by configuration files I control.

![NixOS + Niri Screenshot](https://raw.githubusercontent.com/theopn/haunted-tiles/refs/heads/main/assets/niri-sc.png)

While I spent a considerable amount of time learning the ropes of Nix and NixOS, I'll save the dive into why I love it (as well as its flaws, including why I might eventually drop nix-darwin or home-manager) for another time.

Instead, this post is dedicated to showcasing the new organization scheme I "developed" for my Nix configuration.

## My Previous Pattern

When I first migrated to NixOS and nix-darwin, I used a very simple flake organization scheme:

```
$ tree
.
├── flake.nix
├── home-manager
│   ├── config
│   │   ├── bat.nix
│   │   ├── btop.nix
│   │   ├── ...
│   │   └── a-bunch-of-other-config.nix
│   ├── home.nix            # main import + fallthrough config for home-manager
│   ├── linux.nix           # Linux specific config import
│   └── nixvim
│       ├── ...
│       └── plugins
│           └── ...
└── hosts
    ├── beauvoir                        # Mac Mini
    │   ├── configuration.nix
    │   ├── ...
    │   └── extra-nix-darwin-config.nix
    └── wittgenstein                    # Framework 13
        ├── configuration.nix
        ├── hardware-configuration.nix
        ├── ...
        └── extra-nixos-config.nix
```

In this setup, my flake.nix declared outputs like so:

```nix
nixosConfigurations.wittgenstein = nixpkgs.lib.nixosSystem {
  system = "x86_64-linux";

  modules = [
    nixos-hardware.nixosModules.framework-amd-ai-300-series

    ./hosts/wittgenstein/configuration.nix

    home-manager.nixosModules.home-manager
    (mkHomeManager "theopn")
    {
      home-manager.users.theopn = {
        imports =[
          nixvim.homeModules.nixvim
          ./home-manager/home.nix
          ./home-manager/linux.nix
        ];
      };
    }
  ];

};
```

This is a very classic scheme where files are strictly organized by scope (NixOS, home-manager, nix-darwin).
The biggest benefit of this approach is that it is incredibly easy to understand, especially when adding a new feature.

Let's say I want to add a Tailscale configuration, which needs to live at the NixOS system level.
I would just create a new file at `/hosts/wittgenstein/tailscale.nix` and add it to the imports in `configuration.nix` (or modify `configuration.nix` directly).

`/hots/wittgenstein/tailscale.nix`:

```nix
{ config, pkgs, ... }:
{
  services.tailscale.enable = true;
  networking.nftables.enable = true;
  networking.firewall = {
    enable = true;
    trustedInterfaces = [ "tailscale0" ];
    allowedUDPPorts = [ config.services.tailscale.port ];
  };
  systemd.services.tailscaled.serviceConfig.Environment = [
    "TS_DEBUG_FIREWALL_MODE=nftables"
  ];
}
```

`/hosts/wittgenstein/configuration.nix`:

```nix
{ config, lib, pkgs, ... }:
{
  imports = [
      ./hardware-configuration.nix

       ##### NEW IMPORT STATEMENT #####
      ./tailscale.nix
       ##### NEW IMPORT STATEMENT #####
    ];

  # Boot settings
  boot.loader.systemd-boot.enable = true;
  boot.loader.efi.canTouchEfiVariables = true;
  boot.kernelPackages = pkgs.linuxPackages_latest;

  # rest of configuration.nix
}
```

The exact same idea applies to home-manager; I would make a new file and import it into the main home.nix.

`/home-manager/config/ripgrep.nix`:

```nix
{ ... }:
{
  programs.ripgrep = {
    enable = true;
    arguments = [ "--hidden" "--glob=!.git/" ];
  };
}
```

`/home-manager/home.nix`:

```nix
{ pkgs, lib, config, ... }:

{
  imports = [
    ./config/bat.nix
    ./config/btop.nix
    #...

    ##### NEW IMPORT STATEMENT #####
    ./config/ripgrep.nix
    ##### NEW IMPORT STATEMENT #####
  ];

  home.sessionVariables = {
    XDG_SCREENSHOTS_DIR = "${config.home.homeDirectory}/Pictures";
    XDG_PICTURES_DIR = "${config.home.homeDirectory}/Pictures";
  };

  # rest of home.nix
}

```

Frankly, I like this pattern.
I recommend it to anyone who wants to avoid unnecessary complications in their config.
However, it suffers from one fatal flaw: scattered configuration.

### Problem with the Previous Pattern

Let's say I'm configuring Swaylock.
I can just make `swaylock.nix` with the following config and add it to `home.nix`, right?

`/home-manager/config/swaylock.nix`

```nix
{ config, ... }:
{
  programs.swaylock = {
    enable = true;
    settings = {
      daemonize = true;
      # allows fingerprint sensor -> RET to unlock
      ignore-empty-password = false;
      show-failed-attempts = true;
      show-keyboard-layout = true;

      image = "${config.home.homeDirectory}/.local/share/theoshell/sway/lockscreen.png";
    };
  };
}
```

Nope! You also need to register Swaylock with PAM, which has to happen at the NixOS system level. (Swaylock also has annoying behavior when used with fprintd, so a manual override in the PAM configuration is required).


`/hosts/wittgenstein/swaylock.nix`

```nix
{ ... }:
{
  # register swaylock to /etc/pam.d/
  security.pam.services.swaylock = {
    # https://www.reddit.com/r/NixOS/comments/16oiazf/swaylock_fprintd_fingerprint_reader_issues/
    text = ''
      # Try password first
      auth sufficient pam_unix.so try_first_pass likeauth nullok nodelay
      # Then fprintd
      auth sufficient pam_fprintd.so
      # Fallback
      auth include login
    '';
  };
}
```


Now, the configuration for a single tool is scattered across multiple directories.
This creates a readability and organization problem, especially as you scale up to multiple machines.
You might think importing the home-manager module is enough to get a working setup on a new laptop, only to have it break because you forgot the 5-line system-level PAM config buried in another directory.


## Partial(?) Dendritic Pattern

The [Dendritic pattern](https://github.com/mightyiam/dendritic) by mightyiam has become quite popular within the Nix community - and for good reasons.
While there are a few ways to achieve this architecture, most users use [flake-parts](https://github.com/hercules-ci/flake-parts) to split configurations into modules and pair it with [import-tree](https://github.com/denful/import-tree) to automatically scan and merge them.

This approach offers a lot of benefits, but what I appreciate most is that it allows you to organize Nix files by feature rather than by scope.
It perfectly solves the scattered config problem: you can have a single `modules/swaylock.nix` file that contains everything related to Swaylock.

However, fully adopting the Dendritic pattern comes with its own set of drawbacks, which I will discuss later.

So I found a middle ground.
I keep my core system configurations in the `/hosts` directory, and they are configured exactly how you would set up a standard NixOS/nix-darwin system.
But for all my custom configurations like apps, CLI tools, and window managers, I adopted a "partial" Dendritic pattern using flake-parts and import-tree.


Here is a simplified overview of the resulting tree:

```
$ tree
.
├── flake.nix
├── hosts
│   └── beauvoir
│       └── configuration.nix
│   └── wittgenstein
│       ├── configuration.nix
│       └── hardware-configuration.nix
└── modules
    ├── apps
    │   └── modules...
    ├── cli
    │   └── modules...
    ├── mac
    │   └── modules...
    └── wm
        └── more-modules...
```

### Adding a New Module

Let's go back to the Swaylock example.
With the Dendritic pattern, I can define both the system-level and user-level configurations in a single file:

`modules/wm/swaylock.nix`

```nix
{
  flake.modules.nixos.swaylock = {
    # register swaylock with PAM
    # https://www.reddit.com/r/NixOS/comments/16oiazf/swaylock_fprintd_fingerprint_reader_issues/
    security.pam.services.swaylock = {
      text = ''
        # Try password first
        auth sufficient pam_unix.so try_first_pass likeauth nullok nodelay
        # Then fprintd
        auth sufficient pam_fprintd.so
        # Fallback
        auth include login
      '';
    };
  };

  flake.modules.homeManager.swaylock = { config, ... }: {
    programs.swaylock = {
      enable = true;
      settings = {
        daemonize = true;
        # allows fingerprint sensor -> RET to unlock
        ignore-empty-password = false;
        show-failed-attempts = true;
        show-keyboard-layout = true;

        image = "${config.home.homeDirectory}/.local/share/theoshell/sway/lockscreen.png";
      };
    };
  };
}
```

> [!NOTE]
> Thanks to import-tree, you can name and nest subdirectories inside `modules/` however you please.

If you prefer, instead of creating a dedicated `swaylock` module, you could group it under a broader name (e.g., `flake.modules.homeManager.wm-tools`, bundling it alongside tools like Swayidle and Swaybg).
I typically avoid this, though, because it sacrifices granularity.
If I for some reason decide to swap Swaylock for Hyprlock on just one machine, having them bundled together makes that much harder.


### Adding a New Module & a New System (Flake Output)

Once you've declared a module, adding it to a system is self-explanatory.
Here is a look at the Flake output portion of my `flake.nix`:

```nix
  nixosConfigurations.wittgenstein = nixpkgs.lib.nixosSystem {
    system = "x86_64-linux";
    specialArgs = { inherit inputs; };
    modules = [
      nixos-hardware.nixosModules.framework-amd-ai-300-series

      ./hosts/wittgenstein/configuration.nix
      {
        imports = with self.modules.nixos; [
          ##### added system level PAM settings for swaylock #####
          linux-base niri polkit swaylock
        ];
      }

      home-manager.nixosModules.home-manager
      # Calling a custom home-manager declaration function.
      # Please see my repository for the full `flake.nix`.
      (mkHomeManager {
        saymyname = "theopn"; # you're goddamn right

        theosHomeManagerModules = with self.modules.homeManager; [
          base linux-theme
          ##### added home-manager config module for Swaylock #####
          swaylock
        ];
      })

    ];
  };
};

```

One caveat is that because `nixos.swaylock` and `homeManager.swaylock` belong to two entirely different module systems (even though they are in the same file), I still have to import them in both places.
It is still miles better than having the actual configurations scattered across multiple files, but it is an inefficiency I want to fix eventually.

Looking at this, you can also see how easy it is to add a completely new system.
You just copy and paste a default `configuration.nix` into a new directory under `/hosts`, create a new flake output, and add the modules you need for that specific machine.


## Why Not Full Dendritic Pattern?

The strict Dendritic pattern turns *everything*, including core system configurations, into Flake modules of equal importance.
Everything lives in the `modules/` directory, and your host-specific hardware configuration is no exception.
However, at least to mere casual Nix user like myself, this isn't very intuitive.

Intuitively, enabling a bootloader and installing a kernel package are fundamentally different, and much more system-specific, than configuring a terminal emulator.
I believe core system foundations deserve their own directory and a simpler Nix pattern, one that can still be easily understood (and theoretically function) even if abstractions like import-tree or flake-parts break.

Another problem I have with the full Dendritic pattern is that it can be hard to identify the actual entry points.
In my configuration, every file in the modules/ directory is simply a module, and the only entry point is `flake.nix`.

To summarize the benefits of my partial pattern:

- Easy System Maintenance: Anything in the `/hosts` directory can be easily `diff`ed against the auto-generated `configuration.nix` and `hardware-configuration.nix`, making system-level debugging much easier.
- Clearer File Roles: Every file in the `/modules` directory (unless prefixed by `_`) declares module(s).
    There is no need to guess what a file does or where the entry point is.
- Centralized Overview: All modules are imported directly in `flake.nix`, so you get a clear overview of exactly what features a system contains just by looking at one file.
- Feature Isolation: It preserves the "one file per feature" philosophy of the Dendritic pattern, keeping the `/modules` directory organized.

