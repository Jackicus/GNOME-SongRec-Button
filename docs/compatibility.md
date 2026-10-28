# Compatibility

## GNOME Shell 51 (2026-10-28)

Read: gnome-shell `50.5..51.1` (`js/ui/popupMenu.js`, `js/ui/quickSettings.js`,
`js/ui/messageTray.js`, `js/ui/panel.js`, `js/ui/main.js`, `js/extensions/`, `src/st/`)
and the gjs.guide porting guide for 51.

Nothing the extension uses moved. `PopupMenu.open()` and `close()` take a parameters object
now, and `PopupMenuBase` has an `actor` setter that ties the menu's destruction to its
actor's; the extension calls neither with an animation and its history section still sets
its `actor` and `_delegate` as `PopupSubMenu` does (`docs/private-api.md`). Quick settings
still never destroys a toggle's menu. `QuickMenuToggle`, `SystemIndicator`,
`addExternalIndicator`, `MessageTray.Source` and `Notification` are unchanged.

Booted on GNOME Shell 51.1 (gjs 1.90.1, libadwaita 1.10), NVIDIA GTX 1080, in the nested
shell: enable, a recognition with stand-in `pw-record` and `songrec`, the history, removing
a song, the preferences, and `stop` + `start`. `shell-version` claims 51 only.
