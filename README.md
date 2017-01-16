Introduction
------------

/usr/sbin/fence_ESXi is a STONITH script for Pacemaker <http://clusterlabs.org> to fence VM's running on VMware ESXi 6.x and 5.x (no vCenter required).   It's used by Pacemaker to fence VMs.  Fencing a system is done by powering off or rebooting the sytem.  It's the only way to guarantee release of all resources to prevent possible data corruption.  (for example, two systems try to write to the same filesystem.)

I tested and wrote this device for Pacemaker 1.1 running on RHEL 7.x, but it should run on any version of Pacemaker.  Pacemaker is now the default clustering software included with RedHat 7.x and Centos 7.x.  ssh access must be enabled on the ESXi host!


Installation
------------------------

  You simply copy the script to ALL cluster nodes /usr/sbin/fence_ESXi and set execute permissions.

```
cp fence_ESXi /usr/sbin/fence_ESXi
chown root:root /usr/sbin/fence_ESXi
chmod 755 /usr/sbin/fence_ESXi
```


Testing connectivity
--------------------

  You can test functionality of the fencing script by using these commands.

  To list all VM's on the ESXi host.

```
/usr/sbin/fence_ESXi --ip=192.168.1.95 --username=root --password=XXXXXXXX -o list
dev,21
cl0,24
cl1,25
cl2,29
cl3,31
cl4,32
```

  To get the power status of a VM on the ESXi host.

```
/usr/sbin/fence_ESXi --ip=192.168.1.95 --username=root --password=XXXXXXX -o status -n cl0
```

  To Power ON a VM on the ESXi host.

```
/usr/sbin/fence_ESXi --ip=192.168.1.95 --username=root --password=XXXXXXXX -o on -n cl0
```

  To Power Off a VM on the ESXi host.

```
/usr/sbin/fence_ESXi --ip=192.168.1.95 --username=root --password=XXXXXXXX -o off -n cl0
```

Usage
-----

  To configure fence_ESXi use the High Availability Management tool or the pcs command line tool.  You may want to review the STONITH section of the pacemaker manual to use all the paramaters available..

  <http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Clusters_from_Scratch/ch08.html>

     and

  <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/High_Availability_Add-On_Reference/ch-fencing-HAAR.html>

  NOTE: The script uses the VM's name as it's port name.   If your VM's name is not the same as it's hostname, then you need to map the hostname to it's VM name (the port name). That is done using the Optional Argument "pcmk_host_map".

  Monitor pacemaker log files to watch the status of the fencing script.

```
tail -f /var/log/pacemaker.log |grep stonith
```


GUI
---

```
  Select "Fence Devcies"
  "+ ADD"
  Type Dropdown list "fence_ESXi"
  Fence Instance Name "_USE_ANY_NAME_"
  ipaddr "_IP of ESXi host_"
  login  "root"    (or specify an account on ESXi host with permissions to stop/start VMs)
  port             (leave blank)
  Optional Arguments
  -->pcmk_host_map "_see NOTE below_"
  -->password "_enter_pw_"

  Click "Apply Changes"
```


Command line
------------

```
pcs stonith create _USE_ANY_NAME_ fence_ESXi ipaddr="192.168.1.95" login="root" passwd="XXXXXXXX"
```


Using Opt-in cluster mode
-------------------------

  If you are using symmetric-cluster=true (Opt-IN) cluster, then you must specify which nodes can run the stonith resource. For example: The commands below will set the esxi_stonth resource to run on nodes cl0, cl1 and prefably cl2.

```
pcs constraint location esxi_stonith prefers cl0=10
pcs constraint location esxi_stonith prefers cl1=10
pcs constraint location esxi_stonith prefers cl2=20
```


License
-------

Copyright (C) 2015 Jonathan Senkerik

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.


Support
-------
  Website : http://www.jintegrate.co

  github  : http://github.com/josenk/esxi_STONITH

  Please support my work and efforts contributing to the Linux community.  A $25 payment per server would be highly appreciated.

