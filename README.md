# Linux Developer Provision Scripts

I did this because everytime I reset my PC I forget to install something. This document prevents it from happening again.

Assuming that it ships with a clean Debian 10, this is the roadmap to a working development environment.

##### sudo

Debian doesn't come with sudo by default like Ubuntu, we gonna need it soon.

```shell
su -
apt install -fy sudo
adduser USER sudo
# restart
```

##### Additional Packages

Packages that sooner or later will be needed in this setup.

```shell
sudo apt-get -fy install curl wget git vim zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev apt-transport-https libgdbm-dev libncurses5-dev automake libtool bison dirmngr libgmp-dev pkg-config libgmp-dev libpq-dev ca-certificates gnupg2 lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6
```

##### zsh + spaceship

My favorite shell, so far.

```shell
cd $HOME
sudo apt -fy install zsh curl git fonts-firacode
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# prompts password
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/install.sh)"
edit ~/.zshrc
```

Initial Config

```shell
# ~/.zshrc
# If you come from bash you might have to change your $PATH.
# export PATH=$HOME/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
export ZSH="$HOME/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/robbyrussell/oh-my-zsh/wiki/Themes
ZSH_THEME="spaceship"

# Set list of themes to pick from when loading at random
# Setting this variable when ZSH_THEME=random will cause zsh to load
# a theme from this variable instead of looking in ~/.oh-my-zsh/themes/
# If set to an empty array, this variable will have no effect.
# ZSH_THEME_RANDOM_CANDIDATES=( "robbyrussell" "agnoster" )

# Uncomment the following line to use case-sensitive completion.
# CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion.
# Case-sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"

# Uncomment the following line to disable bi-weekly auto-update checks.
# DISABLE_AUTO_UPDATE="true"

# Uncomment the following line to automatically update without prompting.
# DISABLE_UPDATE_PROMPT="true"

# Uncomment the following line to change how often to auto-update (in days).
export UPDATE_ZSH_DAYS=30

# Uncomment the following line if pasting URLs and other text is messed up.
# DISABLE_MAGIC_FUNCTIONS=true

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# You can set one of the optional three formats:
# "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# or set a custom format using the strftime function format specifications,
# see 'man strftime' for details.
HIST_STAMPS="dd/mm/yyyy"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder

# Which plugins would you like to load?
# Standard plugins can be found in ~/.oh-my-zsh/plugins/*
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
  common-aliases
  docker
  git
  history-substring-search
)

source $ZSH/oh-my-zsh.sh

# User configuration

# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
if [[ -n $SSH_CONNECTION ]]; then
  export EDITOR='vim'
else
  export EDITOR='code'
fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
alias zshconfig="$EDITOR ~/.zshrc"
alias ohmyzsh="$EDITOR ~/.oh-my-zsh"
alias sourcezsh="source ~/.zshrc"
alias grep="grep -i"

export COMPUTER_OWNER_FULL_NAME="Leonardo Falk"
export COMPUTER_OWNER_EMAIL="leonardo.falk@hotmail.com"
export PATH=$PATH:~/.local/bin

# Spaceship Config
# More options at https://github.com/denysdovhan/spaceship-prompt/blob/master/docs/Options.md

SPACESHIP_PROMPT_ORDER=( dir git node ruby )

### Added by Zplugin's installer
source '$HOME/.zplugin/bin/zplugin.zsh'
autoload -Uz _zplugin
(( ${+_comps} )) && _comps[zplugin]=_zplugin
### End of Zplugin's installer chunk

zplugin light zsh-users/zsh-autosuggestions
zplugin light zsh-users/zsh-completions
zplugin light zdharma/fast-syntax-highlighting

```

Then change terminal font do Fira Code.

##### Node Version Manager

NodeJS and npm via nvm.

```shell
sudo apt -fy install git curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
source $HOME/.nvm/nvm.sh
nvm ls-remote && nvm install --lts
```

##### Yarn

```shell
sudo apt -fy install curl
sudo apt remove cmdtest -y # this conflicts with yarn cli
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install --no-install-recommends -y yarn # no install recommended/obsolete nodejs
```

##### Ruby
```shell
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
curl -sSL https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm install 2.6.1
rvm use 2.6.1 --default
ruby -v
rm -f $HOME/.gemrc && touch $HOME/.gemrc && echo "gem: --no-ri --no-rdoc" >> $HOME/.gemrc
rm -f $HOME/.irbrc && touch $HOME/.irbrc && echo "require 'awesome_print'\nAwesomePrint.irb\!\n" >> $HOME/.irbrc
gem install bundler rails
```

Maximizes file system inotify watchers for gem listen.

```shell
echo fs.inotify.max_user_watches=524288 | sudo tee /etc/sysctl.d/40-max-user-watches.conf && sudo sysctl --system
```

##### PostgreSQL

```shell
sudo apt update && sudo apt -fy install postgresql postgresql-common postgresql-client
sudo -u postgres createuser $USER -s
sudo -u postgres psql
# on psql console
\password USER
\q
```

##### Git
```shell
git config --global color.ui true
git config --global user.name "$COMPUTER_OWNER_FULL_NAME"
git config --global user.email "$COMPUTER_OWNER_EMAIL"
git config --global pull.rebase true
ssh-keygen -t rsa -b 4096 -C "$COMPUTER_OWNER_EMAIL"
cat ~/.ssh/id_rsa.pub # copy to https://github.com/settings/ssh/new
ssh -T git@github.com # optional, test connection
```

##### Docker

```shell
sudo apt install -fy curl apt-transport-https ca-certificates gnupg2 software-properties-common
echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker-ce.list
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo apt update && sudo apt install -fy docker-ce docker-ce-cli containerd.io
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

##### VirtualBox

```shell
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb http://download.virtualbox.org/virtualbox/debian xenial contrib"
sudo apt update && sudo apt install -fy virtualbox-5.2
```

##### Java

```shell
echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | sudo tee /etc/apt/sources.list.d/webupd8team-java.list
echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | sudo tee -a /etc/apt/sources.list.d/webupd8team-java.list
sudo apt adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886
sudo apt update
sudo apt install oracle-java8-installer
```

##### Android Studio

Download Android Studio for linux from https://developer.android.com/studio/index.html#downloads as `android-studio.zip`.

```shell
sudo mv android-studio.zip /opt/
sudo unzip android-studio.zip
sudo rm -rf android-studio.zip
sudo chown -R $USER:$USER android-studio
```

##### Spotify

Because music is important too.

```shell
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 931FF8E79F0876134EDDBDCCA87FF9DF48BF1C90
echo deb http://repository.spotify.com stable non-free | sudo tee /etc/apt/sources.list.d/spotify.list
sudo apt update && sudo apt install spotify-client -fy
```

##### Aditional Zsh

```shell
source ~/.zshrc`
zshconfig
```

```shell
# enables calling global packages with yarn
export YARN_GLOBAL_PATH=$(yarn global bin)
export PATH=$PATH:$YARN_GLOBAL_PATH

# Add RVM to PATH for scripting. Make sure this is the last PATH variable change.
export PATH="$PATH:$HOME/.rvm/bin"

# Android Studio Path
export ANDROID_STUDIO_PATH=/opt/android-studio/
export PATH=$PATH:$ANDROID_STUDIO_PATH/bin

# Android Studio SDKs Path
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

# Java Path
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export PATH=$PATH:$JAVA_HOME/bin

# Android Emulator fix
export ANDROID_EMULATOR_USE_SYSTEM_LIBS=1

# Oracle Client
export ORACLE_HOME=/opt/oracle/instantclient_12_1
export LD_LIBRARY_PATH=$ORACLE_HOME:$LD_LIBRARY_PATH
export NLS_LANG="BRAZILIAN PORTUGUESE_BRAZIL.WE8ISO8859P1"

# disable rspec retry
export DISABLE_SPEC_RETRY=1

export W_HOST_PROBUS_SERVER=http://192.168.0.86

export VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

export PATH=$PATH:~/.local/bin

alias grep="grep -i"
```
