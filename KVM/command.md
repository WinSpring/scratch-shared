#to mount a vdi disk image or a qcow image:
sudo apt install qemu-kvm
sudo modprobe nbd --> use this command only if there is no /dev/nbdx, e.g., /dev/nbd0, but check mount to see if a /dev/nbdx is in use.

qemu-nbd -c /dev/nbd0 [path-to-vdi-file or path-to-qcow-file]
sudo fdisk -l /dev/nbd0
sudo partx -a /dev/nbd0 # to tell the kernel the presence of all partitions in /dev/nbd0
sudo mount /dev/nbd0p1 /mnt

#after finishing:
sudo umount /mnt
sudo qemu-nbd -d /dev/nbd0
sudo modprobe -r nbd --> not necessary to remove nbd devices, only when you really don't want to use them anymore.


cp rnp_vms in /etc/init.d
update-rc.d rnp_vms defaults --> to make it run at startup
update-rc.d rnp_vms disable --> to prevent it from running at startup

# resize xen disk image:
Source: https://www.suse.com/support/kb/doc/?id=000016453
##Situation
A XEN virtual machine is configured to use a virtual disk file and its storage needs to be expanded.
##Resolution
1. Stop the virtual machine whose storage needs to be enlarged.
2.  Be smart & safe:  Make a copy of the image file!  Work on the copy, not the original. 
3.  Increase the size of the virtual disk image:

 # dd if=/dev/zero bs=1G count=1 >> your_disk_file

 This example adds 1GB of additional space to the virtual disk image.
4. Resize the filesystem on the disk using the filesystem's resizing tools. In order to make sure the filesystem is clean, force a filesystem check first.
 Here is an example for the ext3 filesystem:

 # e2fsck -f the_disk_file
 # resize2fs the_disk_file

5. Restart the virtual machine



# resize kvm -qcow disk image:
Source: https://computingforgeeks.com/how-to-extend-increase-kvm-virtual-machine-disk-size/
https://computingforgeeks.com/extending-root-filesystem-using-lvm-linux/
https://computingforgeeks.com/resize-ext-and-xfs-root-partition-without-lvm/

1. Shutdown the kvm machine
2. sudo qemu-img resize kvm.qcow +10G
   sudo qemu-img info kvm.qcow
3. Start the kvm machine, use one of the following command to observe from inside the current disk status:
   lsblk
   df -h
   fdisk -l
4. Install cloud utils from inside to use the command growpart
   sudo apt install cloud-guest-utils gdisk
5. Use growpart to extend the partition
   sudo growpart /dev/sda 1 (to increase /dev/sda1 to all available space)
   check with lsblk
6. Resize the file system
   sudo resize2fs /dev/sda1

to escape mousefrom frontend of KVM (QEMU):
Ctrl + left ALT


#convert qcow to vdi for virtualbox: (https://www.dark-hamster.com/server/virtualization/use-qcow2-image-virtualbox/)

qemu-img convert -f qcow2 qcow2_file_name -O vdi vdi_file_name

#convert raw to qcow2: (https://docs.openstack.org/image-guide/convert-images.html)
qemu-img convert -f raw -O qcow2 image.img image.qcow2


#create backing files, which can be used by Xen VMs to save disk space. E.g., 10 Xen VMs can use the same base image (also backing images), only delta is stored in these 10 VMs, not the copy of the whole base image.
Source: https://kashyapc.fedorapeople.org/virt/lc-2012/snapshots-handout.html

server1/disk/qcow2 uses base.qcow2 as backing file: (note: use absolute path, not relative path)
qemu-img create -b /xen/domains/base-img/base.qcow2 -f qcow2 /xen/domains/server1/disk.qcow2
qemu-img info disk.qcow2

in the Xen config file, instead of the normal:
disk = ['file:/xen/domains/pc1/disk.img,xvda1,w']
we use:
disk = ['tap:qcow2:/xen/domains/server1/disk.qcow2,xvda1,w']


