# glusterfs-iscsi
1. OS setup:
- Set hostname.
- Config IP.
- Add hosts file.
- Check time.
- Disable firewall (or allow filewall rules).
- Install packages:
      yum install centos-release-gluster -y
      yum install epel-release -y
      yum install gcc autoconf automake make file libtool libuuid-devel json-c-devel glusterfs-api-devel glusterfs-server tcmu-runner targetcli
      yum install git -y
      git clone https://github.com/gluster/gluster-block.git
      cd gluster-block/
      yum install gluster-block -y
      cat /etc/sysconfig/gluster-blockd
      systemctl daemon-reload
      reboot
      systemctl start gluster-blockd
      systemctl start glusterd
      systemctl enable glusterd
