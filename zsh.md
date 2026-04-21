# ═══════════════════════════════════════════════════════════════
# Powerlevel10k instant prompt — має бути зверху
# ═══════════════════════════════════════════════════════════════
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# ═══════════════════════════════════════════════════════════════
# Oh My Zsh
# ═══════════════════════════════════════════════════════════════
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="powerlevel10k/powerlevel10k"

plugins=(
  git
  zsh-autosuggestions
  sudo
  extract
  command-not-found
  colored-man-pages
  npm
  node
  docker
  docker-compose
  fzf
  z
  zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

# ═══════════════════════════════════════════════════════════════
# Історія команд — прокачка
# ═══════════════════════════════════════════════════════════════
HISTSIZE=50000
SAVEHIST=50000
HISTFILE=~/.zsh_history
setopt EXTENDED_HISTORY
setopt HIST_EXPIRE_DUPS_FIRST
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_IGNORE_SPACE
setopt HIST_VERIFY
setopt SHARE_HISTORY
setopt INC_APPEND_HISTORY

# ═══════════════════════════════════════════════════════════════
# PATH
# ═══════════════════════════════════════════════════════════════
export PATH="$HOME/.local/bin:$PATH"

# ═══════════════════════════════════════════════════════════════
# Node version manager — тільки NVM (fnm закоментований)
# ═══════════════════════════════════════════════════════════════
# FNM_PATH="$HOME/.local/share/fnm"
# if [ -d "$FNM_PATH" ]; then
#   export PATH="$FNM_PATH:$PATH"
#   eval "$(fnm env)"
# fi

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

# Auto-switch Node version on cd
autoload -U add-zsh-hook
load-nvmrc() {
  local nvmrc_path="$(nvm_find_nvmrc)"
  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")
    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$(nvm version)" ]; then
      nvm use
    fi
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc

# ═══════════════════════════════════════════════════════════════
# zoxide — розумний cd
# ═══════════════════════════════════════════════════════════════
if command -v zoxide &> /dev/null; then
  eval "$(zoxide init zsh)"
fi

# ═══════════════════════════════════════════════════════════════
# FZF
# ═══════════════════════════════════════════════════════════════
export FZF_DEFAULT_COMMAND='fd --type f --hidden --exclude .git --exclude node_modules 2>/dev/null || find . -type f'
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
export FZF_CTRL_T_OPTS="--preview 'bat --color=always --line-range :500 {} 2>/dev/null || cat {}' --height 80%"
export FZF_ALT_C_COMMAND='fd --type d --hidden --exclude .git --exclude node_modules 2>/dev/null || find . -type d'
export FZF_CTRL_R_OPTS="--height 40% --reverse"

# ═══════════════════════════════════════════════════════════════
# Aliases — заміни класичних утиліт
# ═══════════════════════════════════════════════════════════════
alias ls="lsd --icon=always --group-directories-first"
alias ll="lsd -l --icon=always --group-directories-first --git"
alias la="lsd -la --icon=always --group-directories-first --git"
alias lt="lsd --tree --depth=2 --icon=always"

if command -v bat &> /dev/null; then
  alias cat='bat --paging=never'
fi

if command -v btop &> /dev/null; then
  alias top='btop'
fi

if command -v duf &> /dev/null; then
  alias df='duf'
fi

# ═══════════════════════════════════════════════════════════════
# Aliases — Git
# ═══════════════════════════════════════════════════════════════
alias gs='git status'
alias gl='git log --oneline --graph --decorate --all -20'
alias gd='git diff'
alias gco='git checkout'
alias gcb='git checkout -b'
alias gp='git push'
alias gpl='git pull'
alias gwip='git add -A && git commit -m "wip"'
alias gundo='git reset HEAD~1 --mixed'
alias gclean='git branch --merged | grep -v "\*\|main\|master\|develop" | xargs -n 1 git branch -d'

# ═══════════════════════════════════════════════════════════════
# Aliases — Node / package managers
# ═══════════════════════════════════════════════════════════════
alias ni='npm install'
alias nr='npm run'
alias nrd='npm run dev'
alias nrb='npm run build'
alias nrt='npm run test'

alias pi='pnpm install'
alias pa='pnpm add'
alias prd='pnpm dev'
alias prb='pnpm build'

alias yi='yarn install'

dev() {
  if [ -f pnpm-lock.yaml ]; then
    pnpm dev
  elif [ -f yarn.lock ]; then
    yarn dev
  elif [ -f bun.lockb ]; then
    bun dev
  else
    npm run dev
  fi
}

# ═══════════════════════════════════════════════════════════════
# Aliases — React Native / Expo
# ═══════════════════════════════════════════════════════════════
alias exs='npx expo start'
alias exc='npx expo start --clear'
alias rna='npx react-native run-android'
alias rni='npx react-native run-ios'
alias ea='eas build'

# ═══════════════════════════════════════════════════════════════
# Aliases — Система Fedora
# ═══════════════════════════════════════════════════════════════
alias update='sudo dnf upgrade --refresh -y && flatpak update -y'
alias ports='ss -tulpn'
alias myip='curl -s ifconfig.me && echo'
alias reload='exec zsh'
alias zshrc='code ~/.zshrc'

alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'

# ═══════════════════════════════════════════════════════════════
# Функції
# ═══════════════════════════════════════════════════════════════

killport() {
  if [ -z "$1" ]; then
    echo "Usage: killport <port>"
    return 1
  fi
  lsof -ti:$1 | xargs kill -9 2>/dev/null && echo "Port $1 freed" || echo "Nothing on port $1"
}

vf() {
  local file
  file=$(fzf --preview 'bat --color=always {} 2>/dev/null || cat {}')
  [ -n "$file" ] && code "$file"
}

vg() {
  if [ -z "$1" ]; then
    echo "Usage: vg <search term>"
    return 1
  fi
  local result
  result=$(rg --line-number --no-heading --color=always "$1" 2>/dev/null | \
    fzf --ansi --preview 'bat --color=always --highlight-line $(echo {} | cut -d: -f2) $(echo {} | cut -d: -f1)')
  [ -n "$result" ] && code -g "$(echo $result | cut -d: -f1):$(echo $result | cut -d: -f2)"
}

fkill() {
  local pid
  pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
  [ -n "$pid" ] && echo $pid | xargs kill -${1:-9}
}

gcof() {
  local branch
  branch=$(git branch --all | grep -v HEAD | fzf | sed 's/.* //' | sed 's#remotes/[^/]*/##')
  [ -n "$branch" ] && git checkout "$branch"
}

cdp() {
  local dir
  dir=$(fd --type d --max-depth 3 . ~/projects 2>/dev/null | fzf)
  [ -n "$dir" ] && cd "$dir"
}


# ═══════════════════════════════════════════════════════════════
# Powerlevel10k конфіг
# ═══════════════════════════════════════════════════════════════
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
