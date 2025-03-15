---
layout: post
nf: https://www.nerdfonts.com/
prev: https://www.programmingfonts.org/#droid-sans
webi: https://webinstall.dev/nerdfont/
---

Every once in a while I come accross a situation where I need to "patch" fonts on a my system. This is usually after a fresh linux install or major kernel updates. You will know something is off with your fonts if strange hieroglyphics start appearing in terminal applications like `neovim`, `fzf`, or `htop`.

The canonincal web source for system fonts is [Nerd Fonts]({{page.nf}}. Fonts from this source provide complete symbol sets needed by modern terminal applications. I usually go with the classic `Droid Sans Mono`, but there are fonts for pretty much any visual preference. You can [preview]({{page.prev}}) fonts before downloading to see how they look with different conditions.

Fonts on Ubuntu based systems live in `.local/share/fonts`. After selecting a font it needs to be downloaded and upzipped into this directory. Once the font is dropped into `/fonts/` the font cache needs to be updated by running `fc-cache -fv`. A short bash snippet below describe the full operation: 

```bash
cd ~/.local/share/fonts
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.3.0/DroidSansMono.zip
unzip DroidSansMono.zip
rm DroidSansMono.zip
fc-cache -fv
```

After completing this operation simply update the font settings in a given terminal emulator to use the patched fonts. Mission complete.
