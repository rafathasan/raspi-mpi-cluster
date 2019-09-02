## Features

- Support Standard Markdown / CommonMark and GFM(GitHub Flavored Markdown);
- Full-featured: Real-time Preview, Image (cross-domain) upload, Preformatted text/Code blocks/Tables insert, Code fold, Search replace, Read only, Themes, Multi-languages, L18n, HTML entities, Code syntax highlighting...;
- Markdown Extras : Support ToC (Table of Contents), Emoji, Task lists, @Links...;
- Compatible with all major browsers (IE8+), compatible Zepto.js and iPad;
- Support identification, interpretation, fliter of the HTML tags;
- Support TeX (LaTeX expressions, Based on KaTeX), Flowchart and Sequence Diagram of Markdown extended syntax;
- Support AMD/CMD (Require.js & Sea.js) Module Loader, and Custom/define editor plugins;
---
##Pre-requisite
- Get yourself a Linux OS. Ubuntu Prefered
- Download the Pre-build Raspbian, [Link](https://drive.google.com/open?id=1ZE4F_4L35xWDauFQV3xF_Dgn57y48GdF)

#### Things are done in pre-build 
MPICH installed.
> $ sudo apt-get update && sudo apt-get install mpich

Change Keyboard layout
> keyboard layout /etc/default/keyboard -> XKBLAYOUT="us"

Disable onboard WiFi
> $ echo "dtoverlay=pi3-disable-wifi" | sudo tee -a /boot/config.txt

Disable Bluetooth

> $ echo "dtoverlay=pi3-disable-bt" | sudo tee -a /boot/config.txt

Set Autologin CLI
> $ sudo raspi-config nonint do_boot_behaviour B2

Set LAN wait on Boot
> $ sudo raspi-config nonint do_boot_wait 1

Start SSH on boot
> $ sudo raspi-config nonint do_ssh 1

### Image Installation

> $ gzip -d mpi_raspbian_lite.img.gz
> $ dd if=clone.img of=/dev/sdX

---

## Setting Up Cluster

### Terminology
- Each Node need to be connected to master Node in order to run the execution command from the master. This will be done via SSH. We are going to override SSH configureation to get rid of human-interaction and make it automate to commnucate seamlessly node to node.
- In order to run the execution command, the source file must exsist on each nodes. Here comes the shared data, we can use NFS Server which stands for network file system. we need to modifiy some of its configuration to allow outbound connection and auto mount the file system in the slave nodes.

>$ sudo
