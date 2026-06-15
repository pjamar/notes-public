# macOS Caps Lock to Hyper with Karabiner-Elements

Karabiner-Elements can map Caps Lock to a Hyper modifier on macOS.

The Hyper convention is usually:

- `shift`
- `control`
- `option`
- `command`

That gives a modifier that is unlikely to conflict with normal app shortcuts. It works well as the base for custom window management, launcher bindings, and other global shortcuts.

The setup is:

1. Install Karabiner-Elements.
2. Open `Simple Modifications`.
3. Map `caps_lock` to a Hyper-style modifier combination.
4. Use the new Hyper key as the base for shortcuts such as `hyper + letter`.

In this setup, `hyper` is the modifier used by Hammerspoon to switch and cycle windows. Once Caps Lock is remapped, the same key becomes the entry point for:

- launching apps
- focusing running apps
- cycling through windows

