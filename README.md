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

