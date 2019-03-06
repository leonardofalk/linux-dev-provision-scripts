# Linux Developer Provision Scripts

I did this because everytime I reset my PC I forget to install something. This document prevents it from happening again.

Assuming that it ships with a clean Debian 9, this is the roadmap to a working development environment.

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

My favorite shell.

```shell
cd $HOME
sudo apt -fy install zsh
chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# prompts password
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
gedit ~/.zshrc
```

Configure

```shell
ZSH_THEME="spaceship"

SPACESHIP_PROMPT_ORDER=( dir git node ruby )

export UPDATE_ZSH_DAYS=15

HIST_STAMPS="dd/mm/yyyy"

export SSH_KEY_PATH="~/.ssh/rsa_id"

export EDITOR='vim' # gedit | nano

alias zshconfig="$EDITOR $HOME/.zshrc"
alias ohmyzsh="$EDITOR $HOME/.oh-my-zsh"
alias sourcezsh="source $HOME/.zshrc"

plugins=(
  rake
  git
  ruby
  gem
  rvm
  docker
  history
  node
  npm
  systemd
  ssh-agent
  sudo
  bundler
  command-not-found
  zsh-syntax-highlighting
  history-substring-search
  gnu-utils
  github
  yarn
  common-aliases
  debian
)
```

more options at https://github.com/denysdovhan/spaceship-prompt/blob/master/docs/Options.md

##### Node Version Manager

NodeJS and npm via nvm.

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source $HOME/.nvm/nvm.sh
nvm ls-remote && nvm install --lts
```

##### Yarn

```shell
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install --no-install-recommends yarn
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

Initial commands for normal and API only apps:

```
# Full
rails new myapp \
  --skip-action-cable \   # skip rails web sockets, cuz it suck balls
  --skip-coffee \         # skip coffeescript, cuz ES5+ is better
  --skip-test \           # skip default test suite, cuz rspec is better
  --skip-system-test \    # skip system tests, cuz capybara is better
  --database=postgresql \ # set a database compatible with most of cloud services
  --webpack=react         # set webpack for react
# API
rails new myapp \
  --api \
  --skip-test \
  --skip-system-test \
  --skip-action-cable \
  --database=postgresql
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
git config --global user.name "Leonardo Falk"
git config --global user.email "email@example.com"
git config --global pull.rebase true
ssh-keygen -t rsa -b 4096 -C "email@example.com"
cat ~/.ssh/id_rsa.pub # copy to https://github.com/settings/ssh/new
ssh -T git@github.com # test connection
```

##### Docker

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
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
