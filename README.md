# Local multi-site webdev environment based on Vagrant via VirtualBox provisioned by Ansible
Vagrant box of choice: [debian/stretch64 (Debian 9)](https://app.vagrantup.com/debian/boxes/stretch64)

Should work on Ubuntu boxes as well.
## What will be installed and configured ⚒️
Generally latest versions available (otherwise specified major.[minor] that matters)
- [Apache 2](https://httpd.apache.org/)
- [PHP 5.6](http://php.net/) ([php5.6-fpm](https://wiki.apache.org/httpd/PHP-FPM))
- [PHP 7.2](http://php.net/) ([php7.2-fpm](https://wiki.apache.org/httpd/PHP-FPM))
  - [Composer](https://getcomposer.org/)
  - [XDebug](https://xdebug.org/)
- [Mysql 5.5.9999 (MariaDB 10)](https://www.mysql.com/)
  - [phpMyAdmin](https://www.phpmyadmin.net/)
- [MailHog](https://github.com/mailhog/MailHog)
- [Node.js 8](https://nodejs.org/)
- [bower](https://bower.io/)
- [curl](https://curl.haxx.se/)
- [eslint](https://eslint.org/)
- [git](https://git-scm.com/)
- [gulp](https://gulpjs.com/)
- [lynx](http://lynx.browser.org/)
- [vim](https://www.vim.org/)
- [vue-cli](https://cli.vuejs.org/)
- [yarn](https://yarnpkg.com/)
- [Midnight Commander](https://midnight-commander.org/)

Guest machine timezone: Europe/Moscow

Guest machine locale: en_US.UTF-8

## Prerequisites ⚠️
- [VirtualBox](https://www.virtualbox.org/) (5.2.22 at the moment)
- [Vagrant](https://www.vagrantup.com/) (2.2.1 at the moment) — Vagrant service and API understanding
  - [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) Vagrant handy plugin which automatically installs the host's VirtualBox Guest Additions on the guest system.

[Ansible](https://www.ansible.com/) (2.7.2 at the moment) is required but not in the list, because Vagrant will take care of this dependency on guest machine. For Debian box installation of Ansible latest version solved by [pip](https://pip.pypa.io/) package installer. Ubuntu does not need it, installation runs via ppa:ansible/ansible repository.

_Noteworthy Windows issue_: box do not properly initialize due to lack of rsync method folders synchronization.

Fix: in file `{Drive|C}:\Users\{your_username}\.vagrant.d\boxes\{box_name}\{box_version}\virtualbox\include\_Vagrantfile` this
```ruby
config.vm.synced_folder ".", "/vagrant", type: "rsync"
```
must be changed to
```ruby
config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
```

## Usage 💻
Consider this directory where it all will be fired up (as example):
`{vagrant_box}` = `~/vagrant_boxes/local-webdev-environment`

```
cd ~/vagrant_boxes
git clone https://github.com/dapertutto/local-webdev-environment.git
```

Sites config in `{vagrant_box}/ansible/roles/webdev/vars/main.yml`

Before provisioning sites' home dirs must physically present in `{vagrant_box}/sites` (`{vagrant_box}/sites/test.dev/www` concerning config) — i.e. create them before the provisioning or else it will fail.
Corresponding domains must be added to the host's machine `hosts` file:
Windows:`\Windows\system32\drivers\etc\hosts`
Linux: `/etc/hosts`
as follows:
```
127.0.0.1   test.loc    www.test.loc
```
After successful `vagrant up` gain access to:
- http://test.dev:8056 — test.dev php 5.6 through php5.6-fpm (and so other sites via port 8056)
- http://test.dev:8072 — test.dev php 7.2 through php7.2-fpm (and so other sites via port 8072)
- http://localhost:8056/phpmyadmin — phpMyAdmin (auth by config, no need to relogin; btw Mysql credential is `root:root`, shoo!)
- http://127.0.0.1:8025 — MailHog mailcatcher service to test email sending locally
- Composer runs as `composer` in shell
- php Xdebug on 9001 port. I'm using Firefox add-on [Xdebug Session Cookie](https://addons.mozilla.org/en-US/firefox/addon/xdebug-session-cookie/) to trigger catching by IDE.

After adding (or removing) sites to *sites* config var in `{vagrant_box}/ansible/roles/webdev/vars/main.yml` don't forget to `vagrant reload --provision` or `vagrant up --provision`, depending on virtual machine state.

**Cheers!**
### May the almighty Cat be with you 😺