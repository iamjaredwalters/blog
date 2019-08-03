---
title: My Terminal
date: '2019-05-22T05:26:13.284Z'
---
 ## Prereq's
Make sure these are installed:
* [Hyper](https://hyper.is/) for terminal
* [Zsh](https://www.zsh.org/) for shell
* [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) for managing zsh configuration 

## Nerd Font
Nerd Fonts are fonts with icons included. Install Hasklig from [Nerdfonts](http://nerdfonts.com/) and tell Hyper about it
```js
// ~/.hyper.js
module.exports = {
  config: {
      // ...
      fontFamily: '"Hasklug NF", "DejaVu Sans Mono", Consolas, "Lucida Console", monospace',
  }
}
```

> Heads up, the font name is called _Hasklig_ on Nerdfonts but installs as _Hasklug NF_ locally

## Pure Prompt
[Pure](https://github.com/sindresorhus/pure) is a minimal zsh prompt. Install the prompt globally via npm

`npm install --global pure-prompt`

Then update `.zshrc` to load the prompt:
```bash
# ~/.zshrc
autoload -U promptinit; promptinit
prompt pure
```

## Z
[Z](https://github.com/agkozak/zsh-z) is a CLI tool that lets you quickly jump to directories that have been frequently visited in the past. 
Follow the [installation instructions](https://github.com/agkozak/zsh-z#for-oh-my-zsh-users) for oh-my-zsh.

## Colors LS
[Color LS](https://github.com/athityakumar/colorls#installation) beautifies the terminal's `ls` command with color and font-awesome icons.

Install the ruby gem
```ruby
gem install colorls
```

And add the `ll` and `l` aliases to `.zshrc` to to use Color LS
```bash
# .zshrc
alias l='colorls --group-directories-first --almost-all'
alias ll='colorls --group-directories-first --almost-all --long' # detailed list view
```

<iframe src='https://gfycat.com/ifr/EsteemedGivingFugu' frameborder='0' scrolling='no' allowfullscreen width='640' height='434'></iframe>

#### Custom Color Scheme
Create a custom color scheme. Use the Color LS dark theme as a basic template:
```bash
cp $(dirname $(gem which colorls))/yaml/dark_colors.yaml ~/.config/colorls/dark_colors.yaml
``` 

> If "no such file or directory" then `mkdir ~/.config/colorls` first.

Next, open `dark_colors.yaml` and replace its contents with
```yaml
# Main Colors
unrecognized_file: lightgray
recognized_file:   white
dir:               blue

# Link
dead_link: red
link:      magenta

# special files
socket:    white
blockdev:  white
chardev:   white

# Access Modes
write:     yellow
read:      gray
exec:      red
no_access: dimgray

# Age
day_old:     white
hour_old:    white
no_modifier: white

# File Size
file_large:  white
file_medium: lightgray
file_small:  darkgray

# Random
report: white
user:   cyan
tree:   dimgray
empty:  yellow
error:  red
normal: darkgray

# Git
addition:     green
modification: yellow
deletion:     red
untracked:    darkgray
unchanged:    white
```

## Hyper Plugins
Configure plugins in `.hyper.js`
```bash
plugins: [
    'hyper-chesterish',
    'hyper-tabs-enhanced',
    'hyperline',
    'hyper-search',
    'hyperterm-paste',
    'hypercwd',
    'hyper-quit',
    'hyper-pane',
],
```

## Bonus 
#### NVM 
Configure zsh to call `nvm use` automatically in any directory with a .nvmrc file.
Add the following to `~/.zshrc`

```bash
# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

