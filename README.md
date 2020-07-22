# glusterfs-iscsi
1. OS setup:
- Set hostname.
- Config IP.
- Add hosts file.
- Check time.
2. Ansible setup: 
- yum install epel-release -y
- yum install git -y
- yum install ansible -y
- vi /etc/ansible/hosts
	[servers]
      gluster01 ansible_ssh_host=<ip> 
      gluster02 ansible_ssh_host=<ip>
      ....
- ssh-keygen
- ssh-copy-id <ip>

3. Run ansible:
- git clone https://github.com/gluster/gluster-block.git
- cd gluster-block/ansible
- vi playbook.yml
- delete all initiator role
- ansible-playbook playbook.yml.

4. Setup gluster store:
- mkfs.xfs /dev/sdb
- mkdir -p /data/brick1
- echo "/dev/sdb /data/brick1 xfs defaults 0 0" >> /etc/fstab
- mount -a
- gluster peer probe <hostname>
# Create volume type Distributed Replicate
- gluster volume create hosting-volume replica 3 gluster01:/data/brick1/brick gluster02:/data/brick1/brick gluster03:/data/brick1/brick
# Create volume type Replicate over 1 brick
- gluster volume create hosting-volume replica 4 transport tcp gluster01:/data/brick1/brick gluster02:/data/brick1/brick gluster01:/data/brick2/brick gluster02:/data/brick2/brick force
------------------------------------------------------------
- gluster vol set hosting-volume group gluster-block
- gluster volume start hosting-volume
- gluster-block create hosting-volume/block-volume ha 3 192.168.1.11,192.168.1.12,192.168.1.13 1GiB
- gluster-block list hosting-volume
- gluster-block info hosting-volume/block-volume

5. Setup client:
- yum install iscsi-initiator-utils device-mapper-multipath
- systemctl start iscsid.service
- systemctl enable iscsid.service
- lsblk
- modprobe dm_multipath
- mpathconf --enable
- vi /etc/multipathd.conf
# LIO iSCSI
devices {
        device {
                vendor "LIO-ORG"
                user_friendly_names "yes" # names like mpatha
                path_grouping_policy "failover" # one path per group
                path_selector "round-robin 0"
                failback immediate
                path_checker "tur"
                prio "const"
                no_path_retry 120
                rr_weight "uniform"
        }
}
- systemctl restart multipathd
- systemctl enable multipathd

Discovery ...
# iscsiadm -m discovery -t st -p 192.168.1.11

Login ...
# iscsiadm -m node -T "iqn.2016-12.org.gluster-block:aafea465-9167-4880-b37c-2c36db8562ea" -l

# lsblk (note the new devices, let's say sdb, sdc and sdd multipath to mpatha)
# mkfs.xfs /dev/mapper/mpatha
# mount /dev/mapper/mpatha /data


On the Server/Target node[s] to resize the block volume
Resizing 1G gluster block volume 'block-volume' to 2G
# gluster-block modify hosting-volume/block-volume size 2GiB
IQN: iqn.2016-12.org.gluster-block:aafea465-9167-4880-b37c-2c36db8562ea
SIZE: 2.0 GiB
SUCCESSFUL ON: 192.168.1.11 192.168.1.12 192.168.1.13
RESULT: SUCCESS
On Initiator side, commands to refresh the device after block volume resizing
Rescan the devices
# iscsiadm -m node -R

Rescan the multipath
# multipathd -k'resize map mpatha'

Grow the filesystem
# xfs_growfs  /mnt
Deleting the block volume
On client node
# umount /mnt
# iscsiadm -m node -u

On the server node
# gluster-block delete hosting-volume/block-volume
SUCCESSFUL ON: 192.168.1.11 192.168.1.12 192.168.1.13
RESULT: SUCCESS
