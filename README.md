# pkgs
`pkgs` is a very basic wrapper for package managers. It relies on [fzf](https://github.com/junegunn/fzf) to provide a simple and easy interface for various package managers in the Linux world.

It aims to improve usability in this area of command line interfaces for package management.

# Usage

The program `pkgs` has five subcommands:

- `install`
- `remove`
- `details`
- `update`
- `no-orphans`

The usage is:

```sh
pkgs <command> "search term"
```
when `<command>` is any of `install`, `remove` or `details`, and:

```sh
pkgs <command>
```
when `<command>` is `update` or `no-orphans`.

For the first three subcommands, `pkgs` provides a fuzzy finder for packages you want to act upon. You specify an initial term to search, and then you can filter results with the fuzzy finder. For example, suppose you want to install Kdenlive, but don't remember its exact name. You only remember it's a video editing tool from KDE. So you can just type:

```sh
sudo pkgs install video
```

Then, thanks to the fuzzy finder, you can start typing to filter the list that appears. For example, if you type `kde`, chances are that you can finally find the `kdenlive` package. Then, it's just a matter of hitting `Enter` and the package will be installed.

If you want to install more then one package, you can hit `Tab` to select each one, then hit `Enter` to confirm.

![Screenshot](https://user.fm/files/v2-e11831b58cfd74792cedcd8c1f02e061/pkgs-install.png)

## pkgs install

The command `pkgs install` installs the packages you select with the fuzzy finder.

```sh
pkgs install [initial search term]
```
## pkgs remove

The command `pkgs remove` uninstalls the selected packages. It works like `pkgs install`:

```sh
pkgs remove [initial search term]
```

## pkgs details

The command `pkgs details` is used to retrieve detailed information about packages. It works like `pkgs install`:

```sh
pkgs details [initial search term]
```

It uses `fzf` to filter the results like `pkgs install`.


## pkgs update

The command `pkgs update` updates all installed packages that have new versions in the repository. Like `pkgs remove_unnused`, no interactive step nor search term are needed.

```sh
pkgs update
```

## pkgs no-orphans

The command `pkgs no-orphans` uninstalls packages that were installed automatically as dependencies of other packages but are not needed anymore (normally because the original packages that trigger the installation were removed). It doesn't need an interactive step or a search term.

```sh
pkgs no-orphans
```

# Which distros are supported?

Currently, the supported distributions are:
- Solus;
- Archlinux-based distros;
- Debian-based distros.

But it's very easy to add support for other distros, even if you don't know [lua](https://www.lua.org/) (the language used to write the program). Take a look at the `config` section of the `pkgs` script.  You will also find a section for translations there if you want to help.

# Installation

By now, there isn't an installation script. But you can simply take the `pkgs` file in this repository and place it in your `PATH`. You must have both [lua](https://www.lua.org/) and [fzf](https://github.com/junegunn/fzf) installed in your system.

There's also an autocompletion script for the fish shell. If you want to use it, just place the `pkgs.fish` file inside the `~/.config/fish/completions` directory. Since I don't use other shells (why do you? =D), I didn't make autocompletion scripts for them. But contributions are welcome.

# Why?

There are two problems with current package managers I want to address: the first one is that each package manager has a unique interface, different from the others and, since I frequently manage computers with different distributions, it's hard to memorise each one; second, all of them have at least some annoying usability problems. Hopefully, `pkgs` can help solving some of these problems.

But, “what kind of usability problems you see in current package managers?” you may ask. Well, it depends on the program. Let's see:

## A problem common to them all: lack of discoverability

The first usability bug I see is that you need to somehow know the exact name of the package you want to install, otherwise you can't use the `install` option. Because normally I don't know or don't remember the exact name (specially because each distribution has different names for the same package), I need to query the package database in some way. If you use a shell with autosugestions like `fish` then the query can, at some extent, be done at the same step of the installation process. But, even with the `Ctrl+S` functionality of `fish`, it can be hard to find the name you are looking for and so you usually do it in two steps: first, query the database using a specific command; then, after finding the package name, use another command to install it.

This problem is even worse with Debian based distributions, since the program used to query the database (`apt-cache`) is different from the program used to install packages (`apt-get`). And it doesn't stop here: the names chosen for these programs are a usability bug by themselves. Think for a moment in the steps you take in a normal installation:

- you need to query the database, so you start typing the first letters of the name of the first program (`apt-cache`), then hit `Tab` to let the shell autocompletes it for you. At this time, you have a break of expectations, realising the autocompletion got stuck at the `apt-` portion of the name since, well, the are two programs with the same prefix;

- the second step is, then, the disambiguation: you tell the shell the program you really want is `apt-cache`;

- the next step is the real query;

- now, once you have found the package name, you proceed with the installation, typing the first letters of the name of the second program (`apt-get`) and hitting `Tab` to let the autocompletion does its magic. Then, once again, you get stuck at the same annoying `apt-` portion of the name, requiring an extra step of disambiguation.

The `apt` program tries to solve at least this problem, offering a common abstraction for the underlying two programs, but it seems it still is a work in progress.

## Updating packages is a two step operation

The updating task is done in two steps in many of the package managers. See, for example, `apt-get`:

```sh
apt-get update
apt-get upgrade
```

The same for `zipper`:

```sh
zipper refresh
zipper update
```

And then you discover that the `zipper`'s `update` option doesn't do the same as `apt-get`'s.

## The `pacman` case

Well, well, `pacman`... What can I say about you? It's a great example of how not to design a user interface. First of all, it's totally counterintuitive. Can you guess what the following commands do?

```sh
pacman -Suy
pacman -Qtdq
pacman -Rns something
pacman -S something
pacman -Si something
pacman -Ss something
pacman -Qs something
```

I think you got it.

Being counterintuitive means not only it's hard to *learn*, but also hard to *memorise*. And, being hard to *memorise* means I have to *learn* again and again commands I don't use frequently.

There's another related problem with this design: it doesn't play well with autosugestions, making discoverability even harder. Even if the shell completes options like `-Ssq` for the user, what's the meaning of this option? How can the user be helped in the process of learning to use the program? Learn as you play isn't an option at all.
