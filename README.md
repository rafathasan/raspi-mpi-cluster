## Pre-requisite
- Get yourself a Linux OS. Ubuntu Prefered
- Download the Pre-build Raspbian, [Link](https://drive.google.com/open?id=1ZE4F_4L35xWDauFQV3xF_Dgn57y48GdF)

#### Things are done in pre-build raspbian
MPICH installed.
> $ sudo apt-get update && sudo apt-get install mpich

Change Keyboard layout
> keyboard layout /etc/default/keyboard -> XKBLAYOUT="us"

Disable onboard WiFi
> $ echo "dtoverlay=pi3-disable-wifi" | sudo tee -a /boot/config.txt

Disable Bluetooth

> $ echo "dtoverlay=pi3-disable-bt" | sudo tee -a /boot/config.txt

Disable systemd service UART

> sudo systemctl disable hciuart

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

#### Setting up Master Node

Install NFS Server on Master
> $ sudo apt-get install nfs-server

Make shared Folder in Home Directory
> $ mkdir shared

Change Directory Permission
> $ sudo chmod -R 777 shared

Set shared folder on NFS Server
> $ echo "/home/pi/shared nfs 192.168.0.100(rw, sync, no_root_squash,no_subtree_check)" >> /etc/exports

Create a List of IPs of Nodes -> ~/nodes.txt
```
192.168.0.100
192.168.0.101
192.168.0.102
192.168.0.103
192.168.0.104
192.168.0.105
```

Set Node Hostname
> $ nano /etc/hosts

```
192.168.0.100 master
192.168.0.101 node1
192.168.0.102 node2
192.168.0.103 node3
192.168.0.104 node4
192.168.0.105 node5
```

Create SSH Master key  
Hit Enter Do not Enter Passphase
> $ ssh-keygen

#### Setting up Slave Node (Do this on Each Slave Node)

Set Master Node Hostname
> $ echo"192.168.0.100 master" >> /etc/hosts

Create shared directory
> $ mkdir shared

Setting Up Auto Mount on Boot
> $ echo "master:/home/pi/shared /home/pi/shared nfs rwx 0 0"

Reboot Slave Node
> $ sudo reboot

Check if the NFS Diretory Mounted  
check for 'master' hostname
> $ df -h

Copy Master SSH key to Slave  
Enter Password if Necessery
> $ scp master:/home/pi/.ssh/id_rsa /home/pi/.ssh/authorized_keys

#### Testing MPI Cluster on Master

Create machinefile
> nano machinefile
```
master
node1
node2
node3
node4
node5
```

Check if All node repond
> $ mpiexec -f machinefile -n 6 hostname

you will the output as
```
master
node1
node2
node3
node4
node5
```
