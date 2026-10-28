---
name: drive-extension
description: See SongRec Button in a throwaway nested GNOME Shell - its quick settings pill, a recognition, the history and its preferences - and take screenshots of it. Use whenever a change to it must be seen or needs a fresh shell start (extension.js, metadata.json, the schema).
---

# Driving SongRec Button in a nested shell

**Read `gnome-ext:nested-shell` first**: the loop (`start`, `do`, `reload`, `stop`), the
steps and its settings are there. This is what is particular to SongRec Button.

```bash
./scripts/nested.sh start --headless     # SongRec Button is ACTIVE when it returns
./scripts/nested.sh run timeout 5 gsettings --schemadir src/schemas set org.gnome.shell.extensions.songrec-button click-action copy
./scripts/nested.sh do "click 1540 16" "wait 1" "click 1557 285" "wait 1" "shot $S/menu.png 1100 0 500 600"
./scripts/nested.sh stop
```

Settings are always that `run timeout 5 gsettings` line written out: a command kept in a
variable (`$G set …`) does not word-split in zsh.

## Where things are (1600x900, one monitor)

- **Quick settings**: open them at about (1540, 16). A `do` that ends with them open leaves
  them open for the next, so a second click there closes them.
- **The pill**: "SongRec", bottom right of the toggles; clicking it starts and stops a
  recognition. Where it sits depends on the look: about (1480, 276) under a plain `start` or
  `--clean` (the real session's look), about (1460, 290) under `--stand-in` (the default
  look). **(1480, 285) hits it in both**; x 1400 is Do Not Disturb. **Its arrow** is at x
  1557 on the same row ((1557, 285) in both) and opens the history below it; each song's ×
  is at its right edge, about x 1532, and Settings is last. Another look (fonts, scale)
  moves them again: with quick settings open, `shot $S/qs.png 1000 0 600 450` and read
  where the pill is before clicking.
- **A real recording** (not the stand-ins) adds the shell's microphone slider under the
  volume one, which moves the toggles down: the pill is then about (1480, 314).
- **Escape** closes the history first and quick settings with a second press.
- **While listening** its icon (a note) shows in the top bar, left of the network icon.

## Before clicking

- **Set `click-action` to `copy`** in the nested settings: `open` launches a browser on the
  real desktop.
- **A recognition is real**: `pw-record` records the machine's own output (or the `device` chosen)
  and SongRec sends its fingerprint to Shazam. With nothing playing it is "No Match".
- **Under `--stand-in` a recognition always fails** ("pw-record: stopped early"): its
  `pw-record` and `songrec` exit at once, for screenshots. For one, use the stand-ins below
  with a plain `start`.

## A real match without playing sound

The nested shell inherits `PATH` from `start` (outside `--stand-in`). A stand-in `pw-record`
that hands over a clip and waits for SIGINT gives a real match:

```bash
ffmpeg -loglevel error -ss 40 -t 8 -i SONG.mp3 -ac 1 -ar 16000 $S/clip.wav   # a CC-licensed track
mkdir -p $S/bin && cat > $S/bin/pw-record <<EOF
#!/bin/sh
for out; do :; done
cp $S/clip.wav "\$out"
trap 'exit 0' INT TERM
while :; do sleep 0.2; done
EOF
chmod +x $S/bin/pw-record
./scripts/nested.sh stop && PATH=$S/bin:$PATH ./scripts/nested.sh start
```

Without reaching Shazam at all, put a stand-in `songrec` beside it that prints a match
(`echo '{"track": {"key": "9", "title": "T", "subtitle": "A", "url": "", "images": {}}}'`);
a `pw-record` that writes `"$@"` to a file shows the arguments a `device` gives.

Kevin MacLeod's tracks on incompetech.com (CC BY) are in Shazam's catalogue.

## History without recognizing

```bash
./scripts/nested.sh run timeout 5 gsettings --schemadir src/schemas set org.gnome.shell.extensions.songrec-button \
    history "[{'key': '1', 'title': 'T', 'artist': 'A', 'cover': '', 'url': '', 'time': '1791187280'}]"
```

## Screenshots

`./scripts/nested.sh shots [--out DIR]` takes the README's
`docs/screenshots/quick-settings.png` (`scripts/nested.d/shots.sh`), under `start --stand-in`: invented songs
with covers drawn by `scripts/nested.d/stand-in.sh`, and `songrec` and `pw-record` as
stand-ins (`EXT_STAND_IN_BINS`), so nothing is recorded or sent. Without `oxipng`, strip
them with `magick IN -strip OUT`. Then the checks in `gnome-ext:screenshots`.
