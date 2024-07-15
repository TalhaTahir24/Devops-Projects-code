<center> # Gluster-FS <center>

```
GlusterFS Setup¶
Requirements¶
GlusterFS is only supported on 64bit systems, so make sure the host machine running GlusterFS and any machines utilising the share is also running on a 64bit system.

This guide is created for Ubuntu 22.04 jammy

Steps to follow¶
Run the following commands on all systems that will be utilising the share, as well as the host system.

Adding Hosts to /etc/hosts¶
We want to make sure our machines can talk to each other via their host names, this can be done by editing and adding the following.

Edit /etc/hosts


sudo nano /etc/hosts
Add in your IP addresses and Hostnames of your machines that will be using GlusterFS, this is my example on my GlusterFS host machine, but remember to add this to all your nodes that will be using GlusterFS


127.0.0.1 localhost
127.0.1.1 elzim

192.168.68.109  elzim
192.168.68.105  pi4lab01
192.168.68.114  pi4lab02
Installing GlusterFS¶
Setup the GlusterFS repository. At the time of writing this, GlusterFS-10 is the latest release.


sudo add-apt-repository ppa:gluster/glusterfs-10
Run an apt update to update the repositories.


sudo apt update
Install GlusterFS


sudo apt install glusterfs-server -y
Start and enable GlusterFS


sudo systemctl start glusterd
sudo systemctl enable glusterd
Peering the Nodes¶
This command is only run on the host machine

Before running the peering command, make sure you run it under sudo via


sudo -s
The following command will peer all the nodes to the GlusterFS Pool, this is using the hostnames for my environment, make sure to change this to suit yours.


gluster peer probe pi4lab01; gluster peer probe pi4lab02;
Running the below command will show your hosts now connected to the pool.


sudo gluster pool list
image.png

Creating the Gluster Volume¶
Let's create the directory that will be used for the GlusterFS volume - This is run on all nodes
Note: You can name "volumes" to anything you like.


sudo mkdir -p /gluster/volumes
Now we can create the volume across the Gluster pool - This is run just on the host


sudo gluster volume create staging-gfs replica 3 elzim:/gluster/volumes pi4lab01:/gluster/volumes pi4lab02:/gluster/volumes force
Start the volume by running the below command


sudo gluster volume start staging-gfs
To ensure the volume automatically mounts on reboot or other circumstances, follow these steps on all machines:

Switch to the root user by running: sudo -s.
Append the following line to the /etc/fstab file using the command: echo 'localhost:/staging-gfs /mnt glusterfs defaults,_netdev,backupvolfile-server=localhost 0 0' >> /etc/fstab.
Mount the GlusterFS volume to the /mnt directory with the command: mount.glusterfs localhost:/staging-gfs /mnt.
Set the ownership of the /mnt directory and its contents to root:docker using: chown -R root:docker /mnt.
Exit the root user session by running: exit.
To verify that the GlusterFS volume is successfully mounted, run the command: df -h.


localhost:/staging-gfs                 15G  4.8G  9.1G  35% /mnt
Files created in the /mnt directory will now show up in the**/gluster/volumes** directorie on every machine.
```