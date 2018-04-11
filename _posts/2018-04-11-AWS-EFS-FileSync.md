---
published: true
---
Amazon EFS File Sync is a tool to copy data from On-premise or in-Cloud file systems to EFS. At the time of this writing EFS File sync only supports network file system (NFS). 

EFS brings a lot of flexibility to the users by allowing the data to grow to almost unlimited capacity as well as allowing developers to mount the file system to unlimited EC2 instances/On-premise virtual machines(using Direct Connect in this case).

For On-premise, EFS file sync is an applicance for your Vmware Esxi hypervisor which can be downloaded at EFS -> File Sync -> create sync agent section. It downloads a .ova file which later can be imported in your hypervisor.

![]({{site.baseurl}}/http://nithinvenugopal.github.io/assets/screenshot.png)


EFS File Sync supports the following hypervisor versions and hosts:

VMware ESXi Hypervisor (version 4.1, 5.0, 5.1, 5.5, 6.0 or 6.5) – A free version of VMware is available on the VMware website. Note that you cannot ssh to this appliance and you may require VMware vSphere client to connect to the host.

￼￼￼￼

Cons
- Only supports NFS/EFS as source
- Files are not synced two ways. Destination is only EFS which means there is no way currently that the EFS file sync tool can let you can bring the data back to NFS.




Other options:
