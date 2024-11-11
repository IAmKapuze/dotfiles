# IAmKapuze dotfiles

## 1. Make own apps dir and add it and `~/.local/bin` to global `PATH`

```sh
mkdir -p ~/apps/bin
sudo sed -i -r 's|(PATH.*)"$|\1:'"$HOME"'/apps/bin:'"$HOME"'/.local/bin"|' /etc/environment
```

## 2. Install packages

```sh
# Ubuntu
sudo apt update
sudo apt install -y atool bat crun fzf git htop podman ripgrep s-tui tmux tree vim w3m xclip zsh
ln -s /usr/bin/batcat ~/apps/bin/bat

# Arch
# TODO
```

## 3. Install and configure `nvim` ([IAmKapuze/dotfiles.nvim](https://github.com/IAmKapuze/dotfiles.nvim))

```sh
wget -P ~/apps https://github.com/neovim/neovim/releases/latest/download/nvim.appimage
chmod 755 ~/apps/nvim.appimage
ln -s ~/apps/nvim.appimage ~/apps/bin/nvim
git clone https://github.com/IAmKapuze/dotfiles.nvim ~/.config/nvim
```

## 4. Install and configure `vifm` [IAmKapuze/dotfiles.vifm](https://github.com/IAmKapuze/dotfiles.vifm)

```sh
wget -P ~/apps https://github.com/vifm/vifm/releases/download/v0.13/vifm-v0.13-x86_64.AppImage
wget -P ~/apps/bin https://raw.githubusercontent.com/vifm/vifm/refs/heads/master/src/vifm-pause
wget -P ~/apps/bin https://raw.githubusercontent.com/vifm/vifm/refs/heads/master/data/vifm-media
chmod 755 ~/apps/vifm-v0.13-x86_64.AppImage ~/apps/bin/vifm-pause ~/apps/bin/vifm-media
ln -s ~/apps/vifm-v0.13-x86_64.AppImage ~/apps/bin/vifm
wget -P ~/.config/vifm https://raw.githubusercontent.com/rockyzhang24/dotfiles/master/.config/vifm/icons
wget -O - https://raw.githubusercontent.com/IAmKapuze/dotfiles.vifm/main/vifmrc.append >> ~/.config/vifm/vifmrc
```

## 5. Configure `zsh`

```sh
# Installing Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# First time config zsh
zsh

# Installing powerlevel10k theme
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
sed -i -r 's/^(ZSH_THEME).*$/\1="powerlevel10k\/powerlevel10k"/' ~/.zshrc

# Installing plugins
# TODO zsh-completions https://github.com/zsh-users/zsh-completions/issues/603
# git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
sed -i -r 's/^(plugins=\(.*)\)$/\1 zsh-autosuggestions zsh-syntax-highlighting fzf\)/' ~/.zshrc

# General configure
sed -i 's/.*\(DISABLE_AUTO_TITLE="true"\)/\1/' ~/.zshrc
echo >> ~/.zshrc
echo "# K" >> ~/.zshrc
echo "export DISPLAY=localhost:10.0" >> ~/.zshrc
echo "export TERM=xterm-256color" >> ~/.zshrc # or TERM=screen-256color for screen
echo "export EDITOR=nvim" >> ~/.zshrc
```

## 6. Auxiliary scripts

```sh
cat > ~/apps/bin/memory-for-screen << EOF
#!/bin/sh
free -h | awk '\$1 == "Mem:" {print \$1" T:" \$2 ", U:" \$3 ", F:" \$4 ", A:" \$7}'
EOF
```

```sh
cat > ~/apps/bin/tmux-memory-cpu-load-print << EOF
#!/bin/sh
echo "[ \$(memory-for-screen) ][ CPU: \`cat /proc/loadavg | awk '{print \$1,\$2,\$3}'\` ]"
EOF
```

```sh
chmod 755 ~/apps/bin/memory-for-screen
chmod 755 ~/apps/bin/tmux-memory-cpu-load-print
```

## 7. Configure `tmux`

```sh
cat > ~/.tmux.conf << EOF
# TODO Add colors
set-window-option -g xterm-keys on
bind-key -n Home send Escape "OH"
bind-key -n End send Escape "OF"
set-option -g history-limit 10000
set-option -g status-style bg=default

set -g status-interval 1

set -g status-left "[ %d-%m-%Y %H:%M:%S ][ "
set -g status-left-length 0

set -g status-right " ]#(tmux-memory-cpu-load-print)"
set -g status-right-length 0
EOF
```
