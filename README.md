# otium360 devenv

Puppet module that sets up the standard otium360 developoment environment.


## Overview
* [Software Requirements](#software-requirements)
* [Basic usage](#basic-usage)
* [Included Software](#included-software)
* [Customization](#customization)
* [Vagrant](#vagrant)
* [Known issues](#known-issues)


## Software Requirements

The only requirement is a Linux Ubuntu/Debian box with internet connection, __otium360 devenv__ will download and install all required software.

Tested on:

  * Ubuntu Trusty 14.04

## Basic usage

  * clone or [dowload](https://github.com/balder-otium360/devenv/archive/master.zip) this repo

        git clone https://github.com/balder-otium360/devenv.git
        # or
        wget https://github.com/balder-otium360/devenv/archive/master.zip
  * change into your devenv repo (we'll assume you have your local copy at `~/git/devenv`)

        cd ~/git/devenv
  * edit `conf/hiera/otium360.json` to fulfill your needs (user account, email, ecc)
  * run the Puppet install script that will install Puppet and required modules:

        ./puppet-install.sh
  * run the Puppet apply script:

        ./puppet-apply.sh
  __otium360 devenv__ will download all the software from the internet, so the whole process may take about 90 minutes with a 10Mbps DSL.

## Included Software

__otium360 devenv__ will install the following software on your machine:

  * Android SDK
  * Compass
  * Git
    - Git-Flow
    - SmartGitHg
    - optional GitHub SSH setup
  * IDEA 13
  * Java
    - Oracle JDK 7
    - Oracle JDK 8 (will be set as default)
  * Maven
  * MongoDB + Robomongo
  * nginx
  * NodeJS + global modules
    - Bower
    - Cordova
    - Grunt
    - Ionic
    - Yeoman
  * Ruby
  * Spring Tool Suite (no tc-server, no Roo)
  * Vagrant
  * Virtual Box
  * WebStorm 8
  * utils
    - Chrome
    - Dropbox
    - Vim
    - Yakuake
    - Puppet Lint
    - Sublime Text 3

## Customization

All current customization is done via Hiera datasources, so it's centralized in a single location. Check `conf/hiera/otium360.json` and edit it to match your environment:

```json
{
  "classes" : [ "devenv" ],

  "devenv::user"        : "otium", // change linux user
  "devenv::home"        : "/home/%{hiera('devenv::user')}",
  "devenv::development" : "%{hiera('devenv::home')}/development",
  "devenv::downloads"   : "%{hiera('devenv::home')}/Downloads",
  "devenv::company"     : "otium360", // change company

  // change installed software
  //   true: install, false: skip install
  "devenv::android"       : true,
  "devenv::aws"           : true,
  "devenv::compass"       : true,
  "devenv::docker"        : true,
  "devenv::git"           : true,
  "devenv::git::smartgit" : true,
  "devenv::idea"          : true,
  "devenv::java"          : true,
  "devenv::maven"         : true,
  "devenv::mongodb"       : true,
  "devenv::nginx"         : true,
  "devenv::nodejs"        : true,
  "devenv::ruby"          : true,
  "devenv::sts"           : true,
  "devenv::utils"         : true,
  "devenv::utils::ant"    : true,
  "devenv::utils::chrome" : true,
  "devenv::utils::dropbox": true,
  "devenv::utils::vim"    : true,
  "devenv::utils::yakuake": false,
  "devenv::utils::puppet_lint"  : true,
  "devenv::utils::sublime_text" : true,
  "devenv::utils::unrar"  : true,
  "devenv::vagrant"       : true,
  "devenv::virtualbox"    : true,
  "devenv::webstorm"      : true,

  // change software versions
  // you might need to tweek file names or download URLs in each class' Puppet (eg. modules/devenv/android.pp)
  "devenv::android::version"          : "r23.0.2-linux",
  "devenv::git::smartgit_version"     : "6_0_7",
  "devenv::idea::version"             : "13.1.5",
  "devenv::java::oracle_java7"        : true,
  "devenv::java::oracle_java8"        : true,
  "devenv::maven::version"            : "3.2.3",
  "devenv::mongodb::robomongo_version": "0.8.3-x86_64",
  "devenv::nodejs::version"           : "v0.10.32",
  "devenv::sts::version"              : "3.6.2.RELEASE",
  "devenv::vagrant::version"          : "1.6.5_x86_64",
  "devenv::virtualbox::version"       : "4.3",
  "devenv::webstorm::version"         : "8.0.4",

  "devenv::git::user_email"     : "otium@%{hiera('devenv::company')}.com", // change Git email
  "devenv::git::user_name"      : "Otium", // change Git name
  "devenv::git::core_editor"    : "vim",  // change Git core editor
  "devenv::git::home"           : "%{hiera('devenv::home')}/git",
  "devenv::git::github_user"    : "%{hiera('devenv::user')}-%{hiera('devenv::company')}",// change GitHub user
  "devenv::git::github_ssh_keys": false,
  "devenv::git::github_ssh_pswd": "s3cr3t" // change GitHub password
}
```

### Version management

__otium360 devenv__ uses really simple patterns to build the package name or donwload URL. Assuming `$version` is defined in Hiera, the following example shows Maven package name and download URL variables.

    $file = "apache-maven-${version}"
    $tgz  = "${file}-bin.tar.gz"
    $url  = "http://ftp.cixug.es/apache/maven/maven-3/${version}/binaries/${tgz}"

In most cases it will be enough to tweak the `conf/hiera/otium360.json` Hiera datasource to change the `devenv::maven::version`, but sometimes you might need to adjust some other values in the associated Puppet file. For example, following with the previous Maven `modules/devenv/manifests/maven.pp`:

    $file = "apache-maven-${version}-mymavensuffix"
    $tgz  = "${file}-myreleasebin.tar.gz"
    $url  = "http://mymavenserver/apache/maven/maven-3/${version}/binaries/${tgz}"


## Vagrant

devenv includes a `Vagrantfile` so you can test all Puppet recipes in a virtual envirionment. The Vagrantfile is ready to use, it will automatically update Puppet to the latest version and will apply the `default.pp` Puppet manifest. To init and provision the Vagrant box use:

    vagrant up

Beware that the Vagrant provisioning will install the full development environment by default, so it can take a while.

If you have already downloaded the required packages on your host, you can create a `Downloads` folder inside the project and check the `Vagrantfile` to use this folder as a file provisioning.

## Known issues

Some of the packages (Sublime Text 3, Google Chrome) require a graphic environment and will throw an error in a headless VM while using default Vagrant setup.
