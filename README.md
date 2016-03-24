# Linux-setup

SSH key generate

1). Use command "ssh-keygen -t rsa -b 4096 -C "your_email@example.com"" on console

2). Then you can sync the codes (public key is located in ~/.ssh/id_rsa.pub)

3). git config --global user.email ""

4). git config --global user.name ""

Linux environment setup

sudo apt-get install libncurses5:i386 libstdc++6:i386 zlib1g:i386 

tftp server setup

Follow the link to set up tftp server: http://askubuntu.com/questions/201505/how-do-i-install-and-run-a-tftp-server 

Flash u-boot image

1) If there is a uboot running on the board, it is easy to update.

a. Configure network (setenv serverip XXX; setenv ipaddr XXX; setenv gatewayipXXX;). If the ethernet doesn’t work, you can use a usb2eth dongle to connect(usb reset; setenv ethact asx0).

b. Put your u-boot.bin to tftp server

c. bubt u-boot.bin

2) if it is a new or fail-to-boot board, you will need to boot from uart. And then flash spi with bubt command.

For new flash uboot image, follow the directions:

- Start the minicom and make sure the you can see the BootROM printings that end with the line "BootROM: Trying UART"

- At this stage the board is ready to get a pattern that instructs it what to do (either start Xmodem or go into a debug shell).

- To star the Xmodem simply send the "Xmodem_boot_pattern.bin" as ascii (Ctrl+A then s, then select ascii).

- After sending the file, you should start seeing strange characters on the screen printed once in a second. These are the Xmodem NACKs.

- This means that you are ready to send the new u-boot with Xmodem (which needs to be with checksum packets and not crc-16 that is used in default Xmodem in minicom).

- Compile the sx-at91.c file (or you can use my pre-built).

- Add this binary to the minicom transfer protocols (see instructions in the file sx-at-91.c header). The newly added file should look like:

               | J  sx-at91    <path to sx-at91 file> <serial port> Y    U    N       N       N    |
         example:
               | J  sx-at91    /home/tawfik/temp/x/sx-at91 /dev/ttyS1 Y    U    N       N       N    |

- Please note that you need to use the correct path and "tty" device. In my example I have the board connected to /dev/ttyS1.

- Start the Xmodem transfer through sending the uart boot file (u-boot-2009.08-2.3.2-uart.bin) and selecting the sx-at91 protocol (Ctrl+a then s, select ax-at91)

- When the transfer finishes, press any key to go back and the u-boot should be loaded. 

• copy out/target/product/abox_gateway/eMMCimg/ to your tftp server /

• Enter u-boot prompt Marvell>>

Execute following u-boot command

• dhcp

• setenv serverip $your_tftp_server_ip

• tftp2emmc –g eMMCimg

Set u-boot enviroment for boot like this

(note: copy kernel and kernel.dtb from out/target/product/abox_gateway/)

Marvell>> editenv bootcmd

tftpboot 0x1000000 kfyao/kernel-3_18_0/kernel;tftpboot 0x2000000 kfyao/kernel-3_18_0/kernel.dtb;bootz 0x1000000 - 0x2000000

Marvell>> editenv bootargs

androidboot.console=ttyS0 console=ttyS0,115200 root=/dev/mmcblk0p3 rw rootwait init=/init panic_debug uart_dma crashkernel=4k@0x8140000 user_debug=31 earlyprintk=uart8250-32bit,0xd4017000 androidboot.lcd=720p cma=20M cgroup_disable=memory ddr_mode=2 RDCA=08140400 cpmem=32M@0x06000000 androidboot.exist.cp=18 androidboot.hardware=armada38x androidboot.selinux=permissive 

Shell access over USB

The Charger presents itself over USB as an Ethernet device with the IP address 172.16.0.10. On an OS X host, it will appear in Network Preferences as "RNDIS/Ethernet Gadget". Shell access can be had by using ssh:

    Download the private key and put it someplace convenient
    Configure the gadget for a manual IP address in the same subnet (eg. 172.16.0.5)
    ssh -i <path_to_private_key> root@172.16.0.10
    additionally you may wish to add the private key to your ssh agent ssh-add <path_to_private_key> and then connect with ssh root@172.16.0.10

Rake Tasks

Take advantage of preconfigured rake tasks to work with Sproutling chargers more easily.

    Install ruby (preferrably with RVM, instructions on website)
    Check ruby installation with ruby -v
    Install bundle with gem install bundle (may need sudo)
    Install required gems with bundle install
    Add private key to ssh agent with ssh-add <path_to_private_key>
    Try connection with charger using rake ssh
    Use rake -T to view all available commands

mac-setup

Repo to document setting up new development machines
iCloud Administration

There are some things we need to do first before we can install anything and that involves tying your computer to your Apple ID. If during the setup you didn't add your Apple ID you should do so now by following the instructions below:

    System Preferences
    iCloud
    Account Details

icloud

    Enter your Apple ID credentials

Now that you have this squared away you can do productive things like listen to music and jam while you go through the rest of the steps
Xcode Install

We love our macs but they need to be made ready to do fun things so we have to install xcode. Luckily terminal is available so you have 2 options:

    Install from the command line
    Install from the App Store

from the command line

$ xcode-select --install

to later agree to the EULA

sudo xcrun cc

You should now see your CLI favorites:

$ which clang
/usr/bin/clang
$ which git
/usr/bin/git
$ 

Configure Git

Avid git lovers even forget that this is a new machine and we will need to configure it so that our commit history is not boofed with a bad user or email address. You will also want to tell git how you are pushing to remote hosts. Set these options in the following manner:

$ git config --global user.name "Your Name"
$ git config --global user.email you@sproutling.com
$ git config --global push.default simple # to save you complaints
$ git config --list
user.name=Wes Bailey
user.email=wes@sproutling.com
push.default=simple
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
remote.origin.url=git@github.com:Sproutling/mac-setup.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master

SSH Setup

In order to access remote servers and Github you will have to generate a public/private key pair:

$ ssh-keygen -t rsa -b 4096 -C "you@sproutling.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/wes/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/wes/.ssh/id_rsa.
Your public key has been saved in /Users/wes/.ssh/id_rsa.pub.
The key fingerprint is:
98:e1:6e:8a:59:d7:56:43:54:f4:2d:6a:eb:5e:11:1d wes@sproutling.com
The key's randomart image is:
+--[ RSA 4096]----+
|          .oo  E |
|         .   . o.|
|      .   .   + o|
|     . + .   . o |
|      + S o o .  |
|     . . . o . . |
|    . + o   . .  |
|   + + .   . .   |
|  o .      .o    |
+-----------------+

VPN Configuration

You will need to send an email to camille@sproutling.com requesting access to the VPN server. He will setup your user and a root certificate authority file. First you must access the VPN server to reset your password:

$ ssh you@hq.sproutling.com
$ Old Password:
$ New Password:
$ Retype New Password:
hq:~ wes$ 

Now you need to add your credentials to your new client machine and phone. To do this go into
Mac

    System Preferences
    Network
    You will see Sproutling HQ as an option

VPN Setup

    Click on it and then click on the Authentication Settings
    Enter your user and password you just setup
    Then connect to test out the connection

iPhone

    Settings
    General
    VPN
    Add VPN Configuration

VPN Phone Setup

    Enter credentials
    Toggle to test connection

To add the root certificate to the allowed list so the tools we use don't complain about the Cert we are using click on the cert in the email you recieve.
Mac

    Keychain will open up
    Install the Cert

iPhone

    Notification will popup
    Install the Cert
    Verified notification will popup

Github Configuration

Now you have to add your public key to your github account. The first thing you will to do is copy your public key to the clipboard:

$ pbcopy < ~/.ssh/id_rsa.pub

Now you can login to your Github account and click on the settings wheel:

SSH Keys

This gives you a listing of current keys and the ability add additional keys by clicking on the Add SSH Key button:

SSH Add

Give it a good name like "Sproutling MBP 2015". I always include the year because you will either change or add machines that are personal and work related because it allows you to delete old keys. In the key contents you can now just paste the contents into the Key field.

SSH Paste

Click Add Key and you are all set to test out whether it works or not. Do that by trying to ssh into a git remote host:

$ ssh -T git@github.com
The authenticity of host 'github.com (192.30.252.129)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
Hi wbailey! You've successfully authenticated, but GitHub does not provide shell access.

If you see the above you are all set. If not then you need to debug that you pasted the right key in your setup. Further information can be had doing:

$ ssh -2vv -T git@github.com
OpenSSH_6.2p2, OSSLShim 0.9.8r 8 Dec 2011
debug1: Reading configuration data /etc/ssh_config
debug1: /etc/ssh_config line 20: Applying options for *
debug2: ssh_connect: needpriv 0
debug1: Connecting to github.com [192.30.252.130] port 22.
debug1: Connection established.
debug1: identity file /Users/wes/.ssh/id_rsa type 1
debug1: identity file /Users/wes/.ssh/id_rsa-cert type -1
debug1: identity file /Users/wes/.ssh/id_dsa type -1
debug1: identity file /Users/wes/.ssh/id_dsa-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
...

Docker

We are using Docker to manage our projects. To get started install docker machine:

docker machine

When the package is available you will want to initialize it:

docker-machine start dev

Makefile

The rest of the documentation includes manual steps to get utilities installed so you can begin work but there is also a Makefile that can be used to control the installation. A complete list of directives at this time includes:

install: make-commons gpg rvm ruby homebrew git mercurial bazaar phantomjs go node npm bower grunt grunt-cli sass gdb
gdb:
homebrew:
gpg:
rvm: gpg
node:
npm:
bower:
grunt:
sass:
log:
git:
mercurial:
bazaar:
phantomjs:
ruby: rvm
go:
python:

The directives are idempotent and can be run individually or collectively via the install directive.
Install Homebrew

This is the package manager of choice but I am security wonk so please do not run scripts that pull content off the web. Instead I have put the script in this repo since it rarely changes so run directly from here using the installed system ruby:

ruby homebrew_install.rb

and then test

$ which brew
/usr/local/bin/brew
$ brew doctor
Your system is ready to brew.
$ brew update
Already up-to-date.

Now you are all set to add things to your mac that you love and can't live without from the linux world:

$ brew install tree
==> Downloading http://mama.indstate.edu/users/ice/tree/src/tree-1.7.0.tgz
######################################################################## 100.0%
==> make prefix=/usr/local/Cellar/tree/1.7.0 MANDIR=/usr/local/Cellar/tree/1.7.0/share/man/man1 CC=clang CFLAGS=-fomit-frame-pointer LDFLAGS= OBJS=tree.o unix.o html.o xml.o has
🍺  /usr/local/Cellar/tree/1.7.0: 7 files, 128K, built in 2 seconds

and because I love Vim:

$ brew install macvim
...

Oh yeah and you might want to wonder about the languages in our stack but we will discuss those later.
Terminal Setup

This would be the time to install iTerm2 if you prefer it. If you are looking for a cool color scheme for the default terminal I recommend IR Black. Installing is quite easy:

$ git clone git@github.com:justincase/IR_Black-OSX.git
...
$ cd IR_Black-OSX
$ open IR_Black.terminal

You can then open the number of tabs you want and save the window group to make this the default on startup. Right click on the terminal and Show Inspector. There you can double click on IR_Black and the Profiles window shows up:

Terminal Profiles

Common things to do here are change the highlight color and set the cursor to block instead of underline. Open up the desired number of tabs and and then save the window group:

save menu

Enter the name and click the Use window group when Terminal starts checkbox:

save dialog

After this is complete you should now see your new Window Group listed in the profiles:

profile listing
Go

This language is a key part of our stack so lets install it and get set up:

schrodinger:~ wes$ brew install go
==> Downloading https://homebrew.bintray.com/bottles/go-1.4.2.yosemite.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring go-1.4.2.yosemite.bottle.1.tar.gz
==> Caveats
As of go 1.2, a valid GOPATH is required to use the `go get` command:
  https://golang.org/doc/code.html#GOPATH

You may wish to add the GOROOT-based install location to your PATH:
  export PATH=$PATH:/usr/local/opt/go/libexec/bin
==> Summary
🍺  /usr/local/Cellar/go/1.4.2: 4566 files, 155M

Now you can install a REPL that can help while you are playing with code:

go get github.com/motemen/gore

I have not figured out to have this as a "system" level package yet so you can add it to your existing projects in Go.
Python

Never rely on the system installed version for our work. Due to the number of machine learning libraries and such we are not on version 3.0 yet.

schrodinger:~ wes$ brew install python
==> Installing dependencies for python: pkg-config, readline, sqlite, gdbm, openssl
==> Installing python dependency: pkg-config
==> Downloading https://homebrew.bintray.com/bottles/pkg-config-0.28.yosemite.bottle.2.tar.gz
######################################################################## 100.0%
==> Pouring pkg-config-0.28.yosemite.bottle.2.tar.gz
🍺  /usr/local/Cellar/pkg-config/0.28: 10 files, 612K
==> Installing python dependency: readline
==> Downloading https://homebrew.bintray.com/bottles/readline-6.3.8.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring readline-6.3.8.yosemite.bottle.tar.gz
==> Caveats
This formula is keg-only, which means it was not symlinked into /usr/local.

...

.app bundles were installed.
Run `brew linkapps python` to symlink these to /Applications.
==> /usr/local/Cellar/python/2.7.10/bin/python -s setup.py --no-user-cfg install --force --verbose --install-scripts=/usr/local/Cellar/python/2.7.10/bin --install-lib=/usr/local
==> /usr/local/Cellar/python/2.7.10/bin/python -s setup.py --no-user-cfg install --force --verbose --install-scripts=/usr/local/Cellar/python/2.7.10/bin --install-lib=/usr/local
==> Summary
🍺  /usr/local/Cellar/python/2.7.10: 6104 files, 98M, built in 105 seconds

Some also prefer using Cask for package/project management:

schrodinger:~ wes$ brew install cask
==> Installing cask dependency: emacs
==> Downloading https://homebrew.bintray.com/bottles/emacs-24.5.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring emacs-24.5.yosemite.bottle.tar.gz
==> Caveats
To have launchd start emacs at login:
    ln -sfv /usr/local/opt/emacs/*.plist ~/Library/LaunchAgents
Then to load emacs now:
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.emacs.plist
==> Summary
🍺  /usr/local/Cellar/emacs/24.5: 3915 files, 105M
==> Installing cask
==> Downloading https://homebrew.bintray.com/bottles/cask-0.7.2.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring cask-0.7.2.yosemite.bottle.tar.gz
==> Caveats
zsh completion has been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺  /usr/local/Cellar/cask/0.7.2: 12 files, 200K

Hostname and Terminal Prompt

Tired of the default hostname for your mac then change it in the sharing preferences:

hostname

Go ahead and give it a cool name of a famous scientist and then you can customize your terminal prompt to be something useful:

$ export PS1="\u@\h:\w> "
wes@schrodinger:~/work/mac-setup> 

We will have a more elaborate prompt available in the next section on Bash but this is good enough for now.
JRE and Java SDK

You shiny new Mac does not come with either of these installed however you will need both to really get some work done given the tools we use. First confirm it is not installed:

java version

If you see the following popup:

java popup

Click on the "more information" button and not the "Ok". If you click on the Ok button then the popup is closed with no action. After clicking on the correct button you will be taken to the Oracle website where you can select the the JDK from the options for OS X and begin the download.
