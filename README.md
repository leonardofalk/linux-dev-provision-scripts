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

##### additional packages

Packages that sooner or later will be needed in this setup.

```shell
sudo apt-get -fy install curl wget git vim zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev apt-transport-https libgdbm-dev libncurses5-dev automake libtool bison libffi-dev dirmngr libgmp-dev pkg-config libgmp-dev libpq-dev ca-certificates gnupg2
```

##### zsh

My favorite shell.

Plugins:

`plugins=(heroku rake git ruby gem ng rvm docker history node npm systemd ssh-agent sudo bundler command-not-found zsh-syntax-highlighting history-substring-search gnu-utils github yarn common-aliases debian)`

```shell
cd $HOME
sudo apt -fy install zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# prompts password
wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf
mkdir -p $HOME/.fonts/ $HOME/.config/fontconfig/conf.d
mv PowerlineSymbols.otf ~/.fonts/
fc-cache -vf ~/.fonts/
mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/
vim ~/.zshrc
```

Make it your default shell (not execute this as root user): 

1. `chsh -s $(which zsh)`

    *Note: that this will not work if Zsh is not in your authorized shells list (/etc/shells) or if you don't have permission to use chsh.
2. Log out and login back again to use your new default shell.


##### Proxy Settings

Depends if I'm under a proxied network.

```shell
export DEFAULT_PROXY=user:pass@10.0.0.1:1000

export HTTP_PROXY=http://$DEFAULT_PROXY
# export HTTPS_PROXY=https://$DEFAULT_PROXY
export FTP_PROXY=$HTTP_PROXY
export NO_PROXY="localhost, 127.0.0.1"

export http_proxy=$HTTP_PROXY
# export https_proxy=$HTTPS_PROXY
export ftp_proxy=$FTP_PROXY
export no_proxy=$NO_PROXY
```

##### Node Version Manager

NodeJS and npm via nvm.

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
source $HOME/.nvm/nvm.sh
nvm ls-remote && nvm install --lts
```

##### Yarn

```shell
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt -fy install yarn
```

Setup yarn global bin path, otherwise global packages won't work.

```shell
# $HOME/.zshrc
export YARN_GLOBAL_PATH=$(yarn global bin)
export PATH=$PATH:$YARN_GLOBAL_PATH
```

##### Ruby
```shell
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm install 2.5.3
rvm use 2.5.3 --default
ruby -v
rm -f $HOME/.gemrc && touch $HOME/.gemrc && echo "gem: --no-ri --no-rdoc" >> $HOME/.gemrc
rm -f $HOME/.irbrc && touch $HOME/.irbrc && echo "require 'awesome_print'\nAwesomePrint.irb\!\n" >> $HOME/.irbrc
gem install bundler rails
```

Initial commands for normal and API only apps:

```
# for full apps
rails new myapp \
  --skip-action-cable \   # skip rails web sockets, cuz it suck balls
  --skip-coffee \         # skip coffeescript, cuz ES5+ is better
  --skip-test \           # skip default test suite, cuz rspec is better
  --skip-system-test \    # skip system tests, cuz capybara is better
  --database=postgresql \ # set a database compatible with most of cloud services
  --webpack=react         # set webpack for react
# or for apis
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

Adds ruby version to the zsh.

```shell
# $HOME/.zshrc
RPROMPT="\$(~/.rvm/bin/rvm-prompt s i v g)%{$fg[yellow]%}[%*]"
```

##### Postgresql

```shell
sudo apt update && sudo apt -fy install postgresql postgresql-common postgresql-client
sudo -u postgres createuser $USER -s
sudo -u postgres psql
# on psql console
\password leonardo
\q
```

##### Git
```shell
git config --global color.ui true
git config --global user.name "Leonardo Falk"
git config --global user.email "email@example.com"
git config --global pull.rebase true
ssh-keygen -t rsa -b 4096 -C "email@example.com"
cat ~/.ssh/id_rsa.pub # and copy to github
ssh -T git@github.com # test connection
```

##### Docker

```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian stretch stable"
sudo apt update && sudo apt install -fy docker-ce
sudo usermod -aG docker $USER
```

##### Docker Compose

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
mkdir -p ~/.zsh/completion
curl -L https://raw.githubusercontent.com/docker/compose/1.21.2/contrib/completion/zsh/_docker-compose > ~/.zsh/completion/_docker-compose
```

##### VirtualBox

```shell
sudo add-apt-repository -y "deb http://download.virtualbox.org/virtualbox/debian stretch contrib"
curl -L https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo apt-key add -
sudo apt-get update && sudo apt-get install -yf virtualbox-5.1
```

#### Java

```shell
echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | sudo tee /etc/apt/sources.list.d/webupd8team-java.list
echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | sudo tee -a /etc/apt/sources.list.d/webupd8team-java.list
sudo apt adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886
sudo apt update
sudo apt install oracle-java8-installer
```

#### Atom Editor

```shell
curl -sL https://packagecloud.io/AtomEditor/atom/gpgkey | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] https://packagecloud.io/AtomEditor/atom/any/ any main" > /etc/apt/sources.list.d/atom.list'
sudo apt update && sudo apt install atom -fy
```

#### Spotify

Because music is important too.

```shell
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 931FF8E79F0876134EDDBDCCA87FF9DF48BF1C90
echo deb http://repository.spotify.com stable non-free | sudo tee /etc/apt/sources.list.d/spotify.list
sudo apt update && sudo apt install spotify-client -fy
```

