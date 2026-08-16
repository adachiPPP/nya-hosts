# nya-hosts

Hosts for **[nya](https://github.com/adachippp/nya)** — nya's git repos.

Each file in this repository is a *host* recipe. A recipe tells nya where to clone a project, how to build it, and what to install.

## Installing a host

```bash
sudo nya host install <name>
```

nya clones the project, runs the instructions, and installs the result using the best strategy it can detect (project install rules, bundle, or single binary). The app's logo is installed into the icon theme automatically, and with `[app]` the app shows up in your application menu.

Remove with:

```bash
sudo nya host remove <name>
```

## Recipe format

```ini
[repo]            # git url to clone (required)
https://github.com/komaruworld/mocktail.git

[folder]          # clone folder name (optional, defaults to the repo name)
mocktail

[instructions]    # shell commands to build (optional)
make build

[binary]          # entry point, relative to the folder (optional - auto-detected)
./build/mocktail

[version]         # package version (optional, defaults to 1)
1.0.0

[desc]            # short description (optional)
Play Roblox on Linux

[app]             # app folder or .desktop file for the application menu (optional)
./packaging
```

## How installs work

1. nya clones `[repo]` into `~/.cache/nya/hosts-build/<name>/`.
2. It runs `[instructions]` inside the folder.
3. nya picks the best install strategy:
   - **Project install rules** — if the project has `make install` or a CMake build, nya stages it with `DESTDIR` and merges the result (like a PKGBUILD).
   - **Bundle** — if the binary sits next to shared libraries or helpers, the whole folder is installed to `/usr/lib/nya/<name>/` and linked into `/usr/bin`.
   - **Single file** — plain binaries are copied straight to `/usr/bin`.
4. Logos are installed into `/usr/share/icons/hicolor/` automatically.
5. With `[app]`, the `.desktop` file is linked into `/usr/share/applications/` and its icons are installed, so the app appears in your application menu.

Everything nya installs is tracked, so `nya remove <name>` cleans up completely.
