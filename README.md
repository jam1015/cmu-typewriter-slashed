# CMU Typewriter Slashed

A fork of CMU Typewriter Text with a slashed zero so `0` is distinguishable
from `O`. Four variants: Regular, Bold, Italic, Bold Italic.

The `.sfd` files are the source of truth; the `.ttf` files are generated
artifacts and must be regenerated whenever the SFDs change.

## Editing the font

1. Edit the SFD in FontForge:

   ```bash
   fontforge ~/cmu-typewriter-slashed/fonts/CMUTypewriterSlashed-Regular.sfd
   ```

2. Re-export every TTF (run from this directory):

   ```bash
   for f in *.sfd; do
     fontforge -lang=ff -c 'Open($1); Generate($2)' "$f" "${f%.sfd}.ttf"
   done
   ```

3. Commit and push the source repo:

   ```bash
   git add -A && git commit -m "..." && git push
   ```

4. Bump the submodule pointer in `~/dotfiles`:

   ```bash
   cd ~/dotfiles/.local/share/fonts/cmu-typewriter-slashed && git pull origin master
   cd ~/dotfiles && git add .local/share/fonts/cmu-typewriter-slashed
   git commit -m "Bump cmu-typewriter-slashed submodule"
   ```

5. Install the new TTFs and refresh the fontconfig cache:

   ```bash
   ~/dots_hooks/install_fonts.bash
   ```

   This script copies every `.ttf` under `~/dotfiles/.local/share/fonts/**/`
   into `~/.local/share/fonts/` and runs `fc-cache -f`.

6. Reload any running apps so they pick up the new glyphs:

   - Waybar:   `pkill -x waybar && nohup waybar >/dev/null 2>&1 & disown`
   - Dunst:    `pkill dunst` (auto-restarts via dbus activation)
   - Kitty / foot / alacritty: open a new window
   - Emacs:    restart the frame

## Fresh-machine install

`~/dots_hooks/hooks_setup.bash` calls `install_fonts.bash` at the end, so on
a freshly cloned dotfiles tree (`git clone --recurse-submodules`) just run:

```bash
~/dots_hooks/hooks_setup.bash
```

## Family renaming

If you ever rename the family (e.g. spin off another variant), update the
SFDs *before* re-exporting so the TTF `name` table stays consistent:

```bash
sed -i 's/OldName/NewName/g' *.sfd
```

Check with `grep` first — a global replace inside an SFD can hit string
literals you didn't mean to touch.
