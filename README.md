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
      yum install glusterfs-server -y
      systemctl start glusterd
      systemctl enable glusterd
      yum install gcc autoconf automake make file libtool libuuid-devel json-c-devel glusterfs-api-devel glusterfs-server tcmu-runner targetcli
      yum install git -y
      git clone https://github.com/gluster/gluster-block.git
      cd gluster-block/
      yum install gluster-block
      ./autogen.sh && ./configure --enable-tirpc=no && make -j install
      cat /etc/sysconfig/gluster-blockd
      systemctl daemon-reload
      reboot
      systemctl start gluster-blockd
      systemctl start glusterd
      systemctl enable glusterd
      ----------------------
      configshell-fb/configshell/__init__.py 
      if __name__ == 'configshell-fb':
    from warnings import warn
    warn("'configshell' package name for configshell-fb is deprecated, please"
         + " instead import 'configshell_fb'", UserWarning, stacklevel=2)

from .console import Console
from .log import Log
from .node import ConfigNode, ExecutionError
from .prefs import Prefs
from .shell import ConfigShell

__version__ = '1.1.25'
__url__ = 'http://github.com/open-iscsi/configshell-fb'
__description__ = 'A framework to implement simple but nice CLIs.'
__license__ = 'Apache 2.0'

iscsiadm --mode node --targetname iqn.2016-12.org.gluster-block:7f2ce50b-ddc2-42a7-ae6e-ec866532ab8c --portal 192.168.1.200:3260 --login
