# Local multi-site webdev environment based on Vagrant via VirtualBox provisioned by Ansible
Vagrant box of choice: [debian/jessie64](https://atlas.hashicorp.com/debian/boxes/jessie64)

Should work on Ubuntu boxes as well.
## What will be installed and configured ⚒️
Generally latest versions available (otherwise specified major.[minor] that matters)
- [Apache 2](https://httpd.apache.org/)
- [PHP 5.6](http://php.net/) ([php-fpm](https://wiki.apache.org/httpd/PHP-FPM))
  - [Composer](https://getcomposer.org/)
  - [XDebug](https://xdebug.org/)
- [Mysql 5.5](https://www.mysql.com/)
  - [phpMyAdmin](https://www.phpmyadmin.net/)
- [MailHog](https://github.com/mailhog/MailHog)
- [Node.js 7](https://nodejs.org/)
  - [bower](https://bower.io/) globally
  - [gulp](http://gulpjs.com/) globally
  - [yarn](https://yarnpkg.com/) globally
- [Midnight Commander](http://www.midnight-commander.org/)
- [git](https://git-scm.com/)
- [curl](https://curl.haxx.se/)
- [lynx](http://lynx.browser.org/)
- [vim](http://www.vim.org/)

bower, gulp, yarn, git, curl, lynx, vim - as is.

Guest machine timezone: Europe/Moscow

## Prerequisites ⚠️
- [VirtualBox](https://www.virtualbox.org/) (5.1.8 at the moment)
- [Vagrant](https://www.vagrantup.com/) (1.8.6 at the moment) — Vagrant service and API understanding
  - [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) Vagrant handy plugin which automatically installs the host's VirtualBox Guest Additions on the guest system.

[Ansible](https://www.ansible.com/) (2.2.0 at the moment) is required but not in the list, because Vagrant will take care of this dependency on guest machine. For Debian box installation of Ansible latest version solved by [pip](https://pip.pypa.io/) package installer. Ubuntu does not need it, installation runs via ppa:ansible/ansible repository.

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

Sites config in `{vagrant_box}/ansible/roles/webdev/vars/main.yml`

Before provisioning sites' home dirs must physically present in `{vagrant_box}/sites` (`{vagrant_box}/sites/test.dev/www` concerning config) — i.e. create them before the provisioning or else it will fail.
Corresponding domains must be added to the host's machine `hosts` file:
Windows:`\Windows\system32\drivers\etc\hosts`
Linux: `/etc/hosts`
as follows:
```
127.0.0.1   test.dev    www.test.dev
```
After successful `vagrant up` gain access to:
- http://test.dev:8080 — test.dev (and so the others via port 8080)
- http://localhost:8080/phpmyadmin — phpMyAdmin (auth by config, no need to relogin; btw Mysql credentials is `root:root`, shoo!)
- http://127.0.0.1:8025 — MailHog mailcatcher service to test email sending locally
- Composer runs as `composer` in shell
- php Xdebug on 9001 port. I'm using nice Firefox add-on [Cute Xdebug](https://addons.mozilla.org/en-US/firefox/addon/cute-xdebug/?src=api) to switch listening on and off with Eclipse IDE

**Cheers!**
### May the almighty Cat be with you 😺