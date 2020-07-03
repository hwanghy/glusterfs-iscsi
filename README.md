# glusterfs-iscsi
2
1. OS setup:
3
​
4
- Set hostname.
5
- Config IP.
6
- Add hosts file.
7
- Check time.
8
- Disable firewall (or allow filewall rules).
9
- Install packages:
10
      yum install centos-release-gluster -y
11
      yum install epel-release -y
12
      yum install gcc autoconf automake make file libtool libuuid-devel json-c-devel glusterfs-api-devel glusterfs-server tcmu-runner targetcli
13
      yum install git -y
14
      git clone https://github.com/gluster/gluster-block.git
15
      cd gluster-block/
16
      yum install gluster-block -y
17
      cat /etc/sysconfig/gluster-blockd
18
      systemctl daemon-reload
19
      reboot
20
      systemctl start gluster-blockd
21
      systemctl start glusterd
22
      systemctl enable glusterd
