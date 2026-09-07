# Windows-style Alt-Tab for Omarchy

A fast, theme-aware overview of every open window, inspired by the familiar
Windows Alt-Tab switcher and built directly for the Omarchy Quattro shell.

![Window switcher preview](preview.png)

## Features

- Shows windows from all regular workspaces in a responsive grid.
- Selects the next window on the first `Alt+Tab` press.
- Cycles while Alt is held and activates on Alt release.
- Supports arrow keys, `Shift+Tab`, Enter, Space, Escape, and the mouse.
- Uses Omarchy's current popup and menu colors automatically.
- Resolves icons through the desktop entry database, with optional custom or
  directly fetched web-app favicons.

## Requirements

- Omarchy 4 (Quattro) or newer
- `omarchy-shell` running on Hyprland

## Install

```sh
omarchy plugin add https://github.com/leandro-3rne/omarchy-window-switcher.git --enable
```

Add the following to `~/.config/hypr/bindings.lua`:

```lua
-- Replace Omarchy's default Alt-Tab bindings with the overview.
hl.unbind("ALT + TAB")
hl.unbind("ALT + SHIFT + TAB")

o.bind(
    "ALT + TAB",
    "Window overview",
    "omarchy-shell shell call io.github.leandro-3rne.window-switcher cycle next"
)

o.bind(
    "ALT + SHIFT + TAB",
    "Window overview (previous)",
    "omarchy-shell shell call io.github.leandro-3rne.window-switcher cycle previous"
)
```

Hyprland reloads the file automatically. Check the result with:

```sh
hyprctl configerrors
```

## Usage

- Hold Alt and tap Tab to move forward.
- Hold Alt and press Shift+Tab to move backward.
- Release Alt, or press Enter/Space, to activate the selected window.
- Use the arrow keys or mouse when the overview is open.
- Press Escape or click the dimmed background to cancel.

You can also summon it directly:

```sh
omarchy-shell shell summon io.github.leandro-3rne.window-switcher '{}'
```

## Optional web-app favicons

Chromium web apps do not always expose a desktop icon that matches their
window class. The plugin therefore includes two opt-in hooks near the top of
`WindowSwitcher.qml`. Remote loading stays disabled by default.

For reliable local icons, place image files somewhere under your home
directory and extend `customWebIcons`:

```qml
property var customWebIcons: ({
  "music.apple.com": Quickshell.env("HOME") + "/.local/share/icons/apple-music.png",
  "mail.example.com": Quickshell.env("HOME") + "/.local/share/icons/example-mail.png"
})
```

The key is the hostname encoded in Chromium's app class. Inspect it with:

```sh
hyprctl clients -j | jq -r '.[] | [.class, .title] | @tsv'
```

Alternatively, set this property to `true`:

```qml
property bool allowRemoteFavicons: true
```

That makes the plugin request `https://HOST/favicon.ico` directly from each
web app's own host. It does not use a third-party favicon service, but the
request still reveals your IP address to that host and some sites do not serve
an icon at that path.

These are source-level customizations. A future `omarchy plugin update` may
replace them, so keep your small mapping somewhere you can reapply it.

## Remove

Remove the two custom bindings from `~/.config/hypr/bindings.lua`, restore any
Alt-Tab bindings you want to keep, then run:

```sh
omarchy plugin remove io.github.leandro-3rne.window-switcher
```

The plugin does not install services, write caches, or modify Hyprland on its
own.

## License

MIT — see [LICENSE](LICENSE).
