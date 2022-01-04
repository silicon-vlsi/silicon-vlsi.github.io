---
sort: 1
---

# IT 
This wiki contains all the details (except the private and proprietary info) for system administrators for the Advanced VLSI Lab at SIT, BBSR.

## LINUX KNOWLEDGEBASE

### STORAGE

**NFS SHARE**

**Important Files for NFS Configuration**
- ``/etc/exports``: Its a main configuration file of NFS, all exported files and directories are defined in this file at the NFS Server end.
- ``/etc/fstab``: To mount a NFS directory on your system across the reboots, we need to make an entry in /etc/fstab.
- ``/etc/sysconfig/nfs``: Configuration file of NFS to control on which port rpc and other services are listening. **NOTE** In our setup, we just use the default options.

**Configuring and starting a NFS Server on CentOS 7**
- **NOTE** The CentOS 7 installation was done with base installation of __File Server with GUI__ so most needed packages were already installed. 
- Install the necessary packages: `#yum -y install nfs-utils`
- **Enable** the __NFS__ services so they start at boot: 
  - `#systemctl enable {nfs-server, rpcbind, nfs-lock, nfs-idmap}`
- **Start** the __NFS__ services: 
  - `#systemctl start {nfs-server, rpcbind, nfs-lock, nfs-idmap}`
- **Note** that most of this service may already be running based on your base installation. So you can check the status and restart the m appropriately:
  - `#systemctl status/restart <service>`
- Add the __share__ directories to `/etc/exports`:

```bash
/home/nfs1      *.vlsi.silicon.ac.in(rw,async,no_subtree_check)
/home/nfs2      *.vlsi.silicon.ac.in(rw,async,no_subtree_check)
```

- The NFS Options:
  - ``rw``: Allows client R/W access.
  - ``async``: default option that should always be used.
  - ``no_subtree_check``: This option prevents the subtree checking. When a shared directory is the subdirectory of a larger file system, nfs performs scans of every directory above it, in order to verify its permissions and details. Disabling the subtree check may increase the reliability of NFS, but reduce security.
  - ``no_root_squash``: Root access allowed for mounted directories. **Dangerous!!** **Note** When root access is necessary, enable it temporarily.

**NFS Client**

- Test mounting the share.. eg. : `#mount -t nfs srv01:/home/nfs1 /home/nfs1`
- If all works add the mounts to `/etc/fstab`:

```bash
srv01:/home/nfs1        /home/nfs1      nfs     noatime,rsize=32768,wsize=32768
srv01:/home/nfs2        /home/nfs2      nfs     noatime,rsize=32768,wsize=32768
```


**RAID**

- RAID 0: taking any number of disks and merging them to one volume.
- RAID 1: Mirroring
- RAID 5/6 : Stripping + Distributed Parity. 
  - Now days RAID 5 is not used in disks larger than 500GB unless they are SSDs or enterprise grade HDDs. [See this article for details](https://www.starwindsoftware.com/blog/raid-5-was-great-until-high-capacity-hdds-came-into-play-but-ssds-restored-its-former-glory-2). Instead RAID 10 is always preferred.
- RAID 10 : Mirroring + Stripping

**PARTIONING**
   
   - When installing CentOS-7, automatic partioning does not work for disk size >2TB so have to choose partion size manually.
   - A good guide on [Recommended Partioning Scheme](https://docs.centos.org/en-US/centos/install-guide/CustomSpoke-x86/#sect-recommended-partitioning-scheme-x86) 
   - Essential partions: `/boot, /(root), /home, swap`
   - Recommended sizes: `/boot (>1G), / (>10G), /home (>1G), swap (see below)`
     - swap size recommendation (assming no hibernation): For RAM size:  * 2-8GB -> Equal to RAM size * 8-64G -> 4G to 0.5xRAM-size


   
### X-SERVER
**XFCE on a CENTOS-7 VIRTUAL MACHINE**
   - **NOTE** `LXDE` display manager is not available on the CentOS repo.
   - Install the [Extra Package of Enterprise Liux (EPEL)](https://docs.fedoraproject.org/en-US/epel/): `$sudo yum install epel-release`
   - Install `XFCE` display manager: `$sudo yum groupinstall xfce`
   
**VNCSERVER on CentOS-7**

- Install `firewalld`, enable it and reboot:
   
```bash
   $sudo yum install firewalld
   $sudo systemctl enable firewalld
   $sudo reboot
```
   
   - Check the firewall running status: `$sudo firewalld --state`
   - Install tigervnc server: `$sudo yum install tigervnc-server` 
   - Login to the user you want the server on and set the passwd: `$vncpasswd`
   - Add a VNC service configuration file by copying an template:
   
```bash
   $sudo cp /lib/systemd/system/vncserver@.service  /etc/systemd/system/vncserver@:1.service
```
   
   - Edit the above service file to replace `<USER>` with the username. 
   - Now start the daemon and enable the service for system wide use:
   
```bash
   $sudo systemctl daemon-reload
   $sudo systemctl start vncserver@:1
   $sudo systemctl status vncserver@:1
   $sudo systemctl enable vncserver@:1
```
   
   - To list the open ports listening to Xvnc: `$ss -tulpn | grep -i vnc`
   - Then allow the appropriate ports in the firewall to access it:
   
```bash
   $sudo firewall-cmd --add-port=5901/tcp
   $sudo firewall-cmd --add-port=5901/tcp --permanent
```
   
   - Probably a good idea to reboot now.
   - Connect using a client (TightVNC/RealVNC/etc) with the Remote Host as `<IP ADDR>:5901` or simply `<IP ADDR>:1`
   
**Resources**
   
   - [Extra Package of Enterprise Liux (EPEL)](https://docs.fedoraproject.org/en-US/epel/)
   - [Installing and configuring a VNC server on CentOS 7](https://serverspace.io/support/help/installing-and-configuring-a-vnc-server-on-centos-7/)
   - [How to Install and Configure VNC Server in CentOS 7](https://www.tecmint.com/install-and-configure-vnc-server-in-centos-7/)
   - [How To Set Up a Firewall Using FirewallD on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7)
   

### REMOTE ACCESS

**PPTP VPN CLIENT ON CentOS-7**

- [See this site](https://zlthinker.github.io/Setup-VPN-on-CentOS) for step-by-step instruction on how to setup a PPTP VPN connection from CentOS 7.
   
### USER MANAGEMENT AND SECURITY

#### FREE IPA
   
[FreeIPA](https://www.freeipa.org) is an integrated Identity and Authentication solution for Linux/UNIX networked environments combining Linux (Fedora), 389 Directory Server, MIT Kerberos, NTP, DNS, Dogtag (Certificate System). It consists of a web interface and command-line administration tools. A FreeIPA server provides centralized authentication, authorization and account information by storing data about user, groups, hosts and other objects necessary to manage the security aspects of a network of computers.
   
**FREE-IPA SERVER INSTALLATION**
   
**CENTOS 7**
   
   - Set the static hostname of the server: `#hostnamectl set-hostname srv01.vlsi.silicon.ac.in`
     - See [documentation](https://www.freeipa.org/page/Deployment_Recommendations) for detail explanation on setting the **host** and **domain** name. The domin should not be the same as the primary domain (`silicon.ac.in`).
   - Set hostname in `/etc/hosts`: `192.168.6.50    srv01.vlsi.silicon.ac.in`
   - Update the OS & reboot: `#yum update; reboot`
   - `#yum install freeipa-server freeipa-server-dns`
   - `#firewall-cmd --add-service=freeipa-ldap`
     - Adding the `freeipa-ldap` should open the necessary ports.
   - Make it permanent: `#firewall-cmd --add-service=freeipa-ldap --permanent`
   - Install the server: `#ipa-server-install`
     - Set the Direct Manager Password. Direct Manager is the super user for managing the IPA server.
     - Set the admin password. Admin is for normal activities such add/edit users.
     - Configure DNS forwarders: **yes**
     - Configure reverse zones: **No**
     - The install ends with the message of opening the ports (already done) 
     - backup the certificate `/root/cacert.p12` to use for replicating the server.
   - Access the FreeIPA admin portal using the URL: `https://srv01.vlsi.silicon.ac.in`
   - If the shared (NFS,SMB,etc.) home directories are exported from the same server, which is the case for us, then we need to take care of two things:
     - If the LDAP (ipa) users home directories are customed ie. not in `/home`. For example: `/home/nfs1` then apply the correct SELinux context and permissions from the `/home` directory to the home directory that is created on the local system eg. `/home/nfs1`:
   
```bash
   # semanage fcontext -a -e /home /home/nfs1
```
   
     - Do the same thing for any other custom home directories.
     - Install, if not already, the `oddjob-mkhomedir` package on the system which provides the `pam_oddjob_mkhomedir.so` library, which the `authconfig` command uses to create home directories. `The pam_oddjob_mkhomedir.so` library, unlike the default `pam_mkhomedir.so` library, can create SELinux labels. The `authconfig` command automatically uses the `pam_oddjob_mkhomedir.so` library if it is available. Otherwise, it will default to using `pam_mkhomedir.so`.
     - Make sure the `oddjobd` service is running: `# systemctl status oddjobd`
     - During the `ipa-server` installation, `ipa-client` is also installed by default without the option `--enablemkhomedir` which is needed to for first login in the server which hosts the home directories so for other clients who mount this directory, they don't need the option. Run the `authconfig` command:

```bash
   # authconfig --enablemkhomedir --update
```
     - Check [this](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system-level_authentication_guide/authconfig-homedirs) man page on redhat.com for custom home dir details.
   
   
**INSTALLING IPA CLIENT**
   
**CENTOS 7**
   
   - Set the hostname: `#hostnamectl set-hostname dt042.vlsi.silicon.ac.in`
   - Add to `/etc/hosts`:
   
```bash
192.168.6.50    srv01.vlsi.silicon.ac.in  srv01
192.168.6.202   dt042.vlsi.silicon.ac.in  dt042
```  

   - Install the client:
   
```bash
   # ipa-client-install --hostname=`hostname -f` \
         --server=srv01.vlsi.silicon.ac.in \
         --domain=vlsi.silicon.ac.in \
         --realm=VLSI.SILICON.AC.IN
```
   - **FIXME** During the install the DNS lookup failed. Changing `/etc/resolv.conf` gets overwritten at boot. Must be a master file that sets.
 
   **Resources**
   - **Server** 
     - [Quick Start Guide -- freeipa](https://www.freeipa.org/page/Quick_Start_Guide)
       - [Deployment Recommendations](https://www.freeipa.org/page/Deployment_Recommendations)
     - [How to Install and Configure FreeIPA on CentOS 7 Server -- linuxtechi](https://www.linuxtechi.com/install-configure-freeipa-centos-7-server/)
     - [How To Install FreeIPA Server on CentOS 7 -- computingforgeeks](https://computingforgeeks.com/install-freeipa-server-centos-7/)
   - **Client**
     - [Install & configure FreeIPA Server & Client on RHEL/CentOS 7 -- golinuxcloud](https://www.golinuxcloud.com/install-freeipa-server-centos-7/)
     - [How To Configure a FreeIPA Client on CentOS 7 -- digital ocean tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-a-freeipa-client-on-centos-7)
     - [How to Install FreeIPA Client on CentOS 7 -- howtoforge](https://www.howtoforge.com/how-to-install-freeipa-client-on-centos-7/)
   - [Nemeth-LinuxSysAdmin-5e-2017] : Ch-8/p243 User Mgmt, p-580 LDAP
   
   
   
#### SELINUX
   
[Security-Enhanced Linux (SELinux)] is a security architecture for Linux systems that allows administrators to have more control over who can access the system.
   
   -  You can tell what your system is supposed to be running at by looking at the `/etc/sysconfig/selinux` file.
     - Default option mode is `enforcing` and policy is `targeted`
   - OR you can use the command `sudo setatus`
   - The mode can be changed in `/etc/selinux/config` eg. `enforced, permissive, disabled`

```note
 When switching from **Disabled** to either **Permissive** or **Enforcing** mode, it is highly recommended that the system be rebooted and the filesystem relabeled(?).  
```
   
   - Resources:
     - [SELinux wiki.centos.org](https://wiki.centos.org/HowTos/SELinux#SELinux_Modes)
     - [What is SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)

## LAUNCHLAB SYSTEM ADMIN

### COMPUTING INFRASTRUCTURE
![Computing Infra](IT-infra.png) 

| **Name** | **IP Address** | **Hardware** | **OS** | **Location** | **Purpose** |
| VLSI-SRV-001 | 192.168.6.50 | Xeon/4C/2.5GHz/16Gb | Redhat 6 | Server Room | File Server |
| VLSI-SRV-002 | 192.168.6.35 | Xeon/20C/40T/2+GHz/64G | CentOS-6.7 | Server Room | Computing Server |
| VLSI-NAS1 | 192.168.5.11 | RaspberryPi-3-B+ 1Gb | OpenMediaVault | Server Room | Archive/Backup Server |
| DT-001->030 | 192.168.51->80 | <FIXME> | Redhat 6 / CentOS 6.10 | Adv VLSI Lab | 30 Workstations |

### STORAGE

**VLSI-SRV-001**

| **Mount** | **Size** | **Purpose** |
| ``swap`` | 8G | 0.5xRAM-size |
| ``/boot`` | 1.5G | Boot files |
| ``/boot/efi`` | 0.5G | EFI boot files |
| ``/(root)`` | 150G | CentOS 7 installation files |
| ``/home`` | 50G | Local home dir |
| ``/var`` | 25G | log,etc |

**VLSI-SRV-001 (OLD) **

| **Mount** | **Size** | **Purpose** |
| ``/PDK`` | 200G | PDK installations |
| ``/home`` | 256G | Faculty home, project directories |
| ``/HOME`` | 60G | student/trainee/etc home |
| ``/CAD`` | 200G | CAD Software Installations |

**Special Sub-directories**
   
| **Subdirectory** | **Purpose** |
| ``/CAD/apps/bin`` | Homegrown scripts |
| ``/CAD/apps/modulefiles`` | Files for the env management tool ``module`` |
| ``/CAD/apps/etc`` | Misc /etc files |
| ``/CAD/licenseServers`` | CAD License files |
| ``/home/NIS/<user>`` | regular user directories |
| ``/home/NIS/faculty`` | faculty user directories |
| ``/home/NIS/administrator`` | admin user directories |
| ``/home/NIS/projects`` | All project directories |

**NOTE** ``/CAD/apps`` is ``git`` repository maintained in `github.com/silicon-vlsi/cad-apps` 

***NFS SHARE***

**Important Files for NFS Configuration**
- ``/etc/exports``: Its a main configuration file of NFS, all exported files and directories are defined in this file at the NFS Server end.
- ``/etc/fstab``: To mount a NFS directory on your system across the reboots, we need to make an entry in /etc/fstab.
- ``/etc/sysconfig/nfs``: Configuration file of NFS to control on which port rpc and other services are listening. **NOTE** In our setup, we just use the default options.

**Configuring and starting a NFS Server**
- You can check this [blog](https://platform9.com/docs/openstack/tutorials-setup-nfs-server)
- Install the necessary packages: ```yum -y install nfs-utils nfs-utils-lib```
- Start the appropriate NFS services:
```bash
chkconfig nfs on
service rpcbind start
service nfs start
service nfslock start  
```
- Start the `rpcbind` on the client: `$ sudo service rpcbind start`
- Export the appropriate directories by configuring in ``/etc/exports``. Our four main shared directories are configured as follows:

```bash
/home/NIS	192.168.6.0/255.255.255.0(rw,async,no_subtree_check)
/PDK		192.168.6.0/255.255.255.0(rw,async,no_subtree_check)
/HOME		192.168.6.0/255.255.255.0(rw,async,no_subtree_check,no_root_squash)
/CAD		192.168.6.0/255.255.255.0(rw,async,no_subtree_check,no_root_squash)
```
   
**NFS Options**:
   
- ``rw``: Allows client R/W access.
- ``async``: default option that should always be used.
- ``no_subtree_check``: This option prevents the subtree checking. When a shared directory is the subdirectory of a larger file system, nfs performs scans of every directory above it, in order to verify its permissions and details. Disabling the subtree check may increase the reliability of NFS, but reduce security.
- ``no_root_squash``: This option allows root to connect to the designated directory. **IMPORTANT** For 64-bit installs that has to be done in ``VLSI-SRV-002``, this option is required so ``root`` in ``SRV-002`` can write to the NFS mounts eg. ``/CAD``.

- Export the direcotries: ```$exportfs -a```
   
**VLSI-NAS1**

**NOTE**: All mount points are relative to ``/srv/dev-disk-by-label-sg2tb``.

**Total Available Space**: 1.8 TB

| Mount | Purpose |
| ``/backup/home`` | Backup user home directories |
| ``/backup/cad`` | Backup CAD |
| ``/backup/pdk`` | Backup PDK |
| ``/archive/home`` | Archive user home directories |
| ``/archive/cad`` | Archive CAD |
| ``/archive/pdk`` | Archive PDK |
| ``/smb-share`` | Samba Share for Windows access |


**Transferring Files (to/From NAS)**

**NOTE**: SSH keys are already setup between ``root`` in ``VLSI-SRV1`` and ``admin-vlsi`` in ``VLSI-NAS1``. So, when using the following commands using ``sudo`` or ``root`` account will not require password authentication.

- To tar a directory over a SSH tunnel
```bash
#tar czf - --exclude=.mozilla --exclude=.trn <dir-to-tar> | \
   ssh admin-vlsi@192.168.5.11 "cat > /srv/dev-disk-by-label-sg2tb/<dir>/<tar-file.tgz>"
```
You can use the script ``/CAD/apps/bin/tar2nas`` for the above function.

- Copy file to NAS server over a SSH tunnel
```bash
#scp <src-file> vlsi-admin@192.168.5.11:/srv/dev-disk-by-label-sg2tb/<dir>/<file>
```

**LOGICAL VOLUME MANAGEMENT (LVM)**
   
**FREQUENTLY USED COMMANDS**

- ``lsblk`` : Lists the tree structure of all storage
- ``vgdisplay <LVGROUP>`` : Displays all details of the LV GROUP
- ``sudo lvcreate -L 1T -n lv_home <LVGROUP>`` : Creates a Logical Volume of 1 TB named ``lv_home``.
- ``sudo mkfs /dev/LVGROUP/lv_name`` : Creates an ``ext4`` filesystem.
- To resize a LVM: (**NOTE** When downsizing the volume, all data was LOST. So BACKUP!. FIXME: Check the same for upsizing.)
  - ``sudo umount <mount dir>
  - ``sudo lvchange -an LVGROUP/lv_name``
  - ``sudo lvresize -L +100G LVGROUP/lv_name``
  - ``sudo lvchange -ay LVGROUP/lv_name``

### SUBVERSION

**Setting up a Subversion Server**
   
- Decided to setup the server on ``VLSI-SRV-002`` so we can install any needed tools.(CentOS 6 is now deprecated so not true anymore)
- Install ``subversion``: ``# yum install subversion``
- Create a user ``svn`` and login as that user.
- Make a directory for all repos: ``$ mkdir repos``
- change directory ``cd`` to ``repos``
- Create a SVN repo: ``$ svnadmin create sevya2019``
- cd to ``sevya2019/conf`` and make ensure the following in ``svnserv.conf``

```bash
[general]
anon-access = read
auth-access = write

password-db = passwd
```

- Which allows non-auth users read-only access and auth users read-write access.
- Open the ``passwd`` file and add the user/password in it. **IMPORTANT** Change permission to ``600`` since password is plain text.
- Create the file ``/etc/sysconfig/svnserve`` and add the following line it:
 - ``OPTIONS="--root /HOME/svn/repos"`` to define the root repository.
- To start the server at boot:``# chkconfig svnserve on``
- To manually start:``# service svnserve start``
- To list a repo:``$svn list svn://192.168.6.35/sevya2019``
- To create another repo in future, ``$svnadmin create <repo>`` in ``/HOME/svn/repos`` and restart the svnserver.

**Checking-out and Using a Repository**
   
- Checkout a repo:``$svn checkout svn://192.168.6.35/<repo-name>``

**FREQUENTLY USED SVN COMMANDS**
- Add a file to repo after creating it: ``$svn add <file>``
- Commit: ``$svn commit -m "Comments"``  **NOTE** If you don't put comments, make sure env var ``SVN_EDITOR`` is set to a valid editor.
- Cancel a commit: ``$ svn revert <file>``

**Checking out the the feynman_svn/feynman_ext_repo**

For **Linux**: 
- Before checking out the SVN repo, the private key (SSH) of the client needs to match the public key on the SVN server. Easiest way is to take the private key from PuTTy (eg. ``id_rsa.pem`` or ``.openssh``) and copy it to ``.ssh/id_rsa`` and the public key can be copied to ``.ssh/id_rsa.pub``

```bash
svn list svn+ssh://svn@54.254.226.43/home/svn/repos/feynman_svn
```

```bash
svn checkout svn+ssh://svn@54.254.226.43/home/svn/repos/feynman_svn
```

### ROOT ACCESS
   
- **Root login is COMPLETELY PROHIBITED** unless absolutely necessary in very few cases, since it is a security threat.
- Please do not start any VNC Servers as root, which is a security threat as well.
- Instead, all root actions are done using ``sudo`` accounts.
- After login in with the sudoer account, if you need to run any command as root, simply precede the command with ``sudo``, eg:
```bash
$sudo <command>
```

**Making an existing user a sudoer**
   
- Login in as root and uncomment the wheel group from /etc/sudoer: ``#visudo``
- ``%wheel  ALL=(ALL)  ALL `` 
 - NOTE The % sign is NOT a comment. 
- Add the user (say ``admlocal``) to the group wheel: 
 - ``#usermod -aG wheel admlocal``

### USER ACCOUNTS

User accounts are broadly divided into three types:
- ``admin``: Administrator accounts with ``sudo`` capacity. Home directories reside in ``/home/NIS/administrator``.
- ``faculty``: Faculty accounts without any special privilege for now. Need to add them to ``faculty`` group with a ``umask=027`` so files are created with permission ``750`` so faculty can share files within the home directories. Home directory in ``/home/NIS/faculty``
- ``users``: Standard users eg. students, course participants, etc. Home directory in ``/home/NIS``

**ADDING USER ACCOUNT USING COMMAND LINE ``useradd``**

```bash
$sudo useradd -s /bin/csh -d /home/NIS/<username> -N -g users -G users \
              -c "Firstname Lastname, Dept., email", -k /etc/skel-student -K UMASK=022 -m <username>
$sudo make -C /var/yp
```
 Where the options for ``useradd`` are:
```bash
-s : Default Linux shell (eg. csh)
-d : Home directory of the user
-N : Do not create a group with the user name but add it to the one with -g option
-g : add user to this group as the initial group (when -N option is provided)
-G : list of supplementary groups the user is going to be part of.
-c : comment. Please provide the comment is the format in the example for
     better user information retrieval when using the 'finger <user>' command
-k : location for the skeleton file eg. .cshrc, etc.
-K : can override any variables from the /etc/login.defs eg. UMASK
-m : create home directory if it doesn't exist.
```

**ADDING BATCH USERS**
Use the script ``/CAD/apps/bin/buseradd`` to add a list of users from a comma-separated file like shown below:
```bash
"Silicon Institute of Technology, Bhubaneswar",,,,,,
VLSI Center of Excellence (COE),,,,,,
List of Students (2017-18),,,,,,
,,,,,,
Sl.No.,Regd No,Name,Branch,Phone No.,E-mail ID,User Name
1,1501209000,G Kumar,ETA,8417136000,ga001@gmail.com,gkumar
2,1501209000,M Ansari,AEI,9171729000,ma001@gmail.com,mansari
3,1501209000,V Sao,ETB,9112411000,vi001@gmail.com,vsao
4,1501209000,N Singhal,ETA,7215101000,ni001@gmail.com,nsinghal
5,1501209000,D Kumari,AEI,9178811100,m001@gmail.com,dkumari
```

The CSV file can be exported directly from a Excel file that is currently in the above standard format.

You can create single user using the above file as well.

**FIXME**: The above description of buseradd needs to be updated to match the changes in the script.

** Limiting Hardisk Usage with *quota* ** (Not yet immplemented)
   
- First the mount point (eg. ``/HOME``) needs to be enabled for quote. See the following [link](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/ch-disk-quotas) for instructions to create a "quotad" mount point. Basic steps are:
 - Enable quotas in the main server's (VLSI-SRV-001) ``/etc/fstab``:
  - ``/dev/mapper/VolGroup03-LogVol00_home            /HOME           ext4    defaults,usrquota,grpquota      1 2``
 - Remount the file system: unmount and mount
 - Make sure the ``/etc/fstabs`` of the NFS clients are updated with the mount point. **NOTE** You don't need to assign the usrquota and grpquota options in the NFS clients.
 - Create the quota database files and generate the disk usage table:
  - Create the quota files: ``#quotacheck -cug /HOME``
  - Generate the diskusage table: ``#quotacheck -vug /HOME``
 - Assign quotas per user:``edquota <username>``

#### NIS SERVER
   
**ADDING A NEW USER USING THE AWS NIS SERVER**

- Login to `aws-ampere` as `centos`
  - `$sudo useradd <options>`
  - `$sudo make -C /var/yp`
- Login to `VLSI-SRV-002` as `admin`
  - `$sudo mkdir /HOME/<user>`
  - `$sudo chown -R <user>:users /HOME/<user>`
  - `$su -l <user>`
  - `$cp /etc/skel/.cshrc`
 
**MIGRATING NIS SERVER**
   
   The description below is for migrating our local NIS server (RHEL 6) to am AWS Lightsail instance (CentOS 7). But most of it is applicable to other systems as well.
  
- First started with this [post](https://serverfault.com/questions/503363/how-do-i-replace-an-nis-master-server).
- **Migrating** ``passwd/groups/shadow/gshadow`` (Mostly manual but some automation can be followed from this [post](https://www.cyberciti.biz/faq/howto-move-migrate-user-accounts-old-to-new-server/) ).
- Transferred all the ``/etc`` stuff to the AWS instance and created temp files ``passwd.mig`` etc.
   - ```awk -v LIMIT=500 -F: '($3>=LIMIT) && ($3!=65534)' passwd > passwd.mig``` This ensures no system accounts are duplicated. Double check manually.
   - ```sudo cat shadow > shadow.mig``` and edit to make sure the same users as in ``passwd``
   - ```awk -v LIMIT=500 -F: '($3>=LIMIT) && ($3!=65534)' group > group.mig``` **NOTE** the ``users`` group got skipped so had to add manually.
   - ```sudo cat gshadow > gshadow.mig``` and edit to make sure the same users as in ``gpasswd``
   - ```cd /etc ; sudo tar -czvf ~/baks/etcpasswd.tgz passwd groups shadow gshadow yp.conf ypserv.conf``` : Backup before adding the new data.
   - ```sudo vim /etc/passwd``` and append ``passwd.mig``
   - ```sudo vim /etc/group``` and append ``group.mig``
   -```sudo chmod 600 /etc/shadow; sudo vim /etc/shadow```, append ``shadow.mig`` and ``chmod 000 /etc/shadow``
   -```sudo chmod 600 /etc/gshadow; sudo vim /etc/shadow```, append ``gshadow.mig`` and ``chmod 000 /etc/gshadow``
   - Check the migration by trying to login as one of the migrated user eg. ``su <user>``
- **SETTING UP NIS SERVER**
   - If not installed, install ``ypserv, ypbind`` : ```sudo yum install ypserv ypbind```
   - Set ``chkconfig`` to start at boot: ```sudo chkconfig ypserv/ypbind on```
   - Backup up ```/var/yp```
   - Copy all the contents of old ``/var/yp`` to the new server.
     - Had to change ``YPBINDIR = /usr/lib/yp`` TO ``YPBINDIR = /usr/lib64/yp``
     - Updated ``/var/yp/ypservers`` with the IP address of the current server.
   - Setup the local ``ypbind``: ```sudo authconfig-tui``` with ``NIS_Silicon`` as the domain and the IP address should be the local IP and **NOT** the public IP of the instance.
     - Alternatively you can add the config line ```domain NIS_Silicon server 172.26.5.80``` in the ``/etc/yp.conf``
   - Run the makefile : ```sido make -C /var/yp```
   - check the domain name ```domainname``` and it should output ```NIS_Silicon```
   - Check the NIS server ```ypwhich``` and it should output the hostname eg. ``ip-172-26-5-80.ap-southeast-1.compute.internal``
   - Check if you can query the passwd and group : ```ypcat passwd``` and ```ypcat group```
   - **IMPORTANT** Login to the AWS Lightsail dashboard and from the Networking tab of the instance, open the following ports:
     - ```TCP:111,834,998``` and ```UDP:111,834,995```
     - TCP:998 and UDP:995 found it from the ypserv port by typing ```rpcinfo -p``` on the new server. The client still could not connect. This [post](https://docs.oracle.com/cd/E37670_01/E41138/html/ch22s05s02.html) suggested the other two and that worked!!
   
- **SETTING UP THE CLIENT**
   - ```sudo authconfig-tui``` and set domain to ``NIS_Silicon`` and server to the public IP address of the instance.
   - You can manually configure it as:
     - add ``NISDOMAIN=NIS_Silicon`` in ``/etc/sysconfig/network``
     - add ``domain NIS_Silicon server <public IP od AWS>`` to ``/etc/yp.conf``
     - Make sure the following lines contain ``nis`` as an option in the file ``/etc/nsswitch.conf`` file: ``passwd: files nis`` ``shadow: files nis`` ``group: files nis`` ``hosts: files nis``  ``dns networks: files nis``  ``protocols: files nis``  ``publickey: nisplus``  ``automount: files nis``  ``netgroup: files nis``  ``aliases: files nisplus``
   - To start and stop the ``ypbind`` service: ``sudo service ypbind start/stop/status``

### VNC SERVER

Currently, vncservers are automatically started for some users ( Check Config file: ``/etc/sysconfig/vncservers`` in VLSI-SRV-002

- To start a VNC server manually, first set a ONE-TIME-ONLY password:
 - ``vncpasswd``
- Then start the vncserver:
 - ``vncserver :<number> -depth 24 -geometry <resolution>`` for example:
 - ``vncserver :10 -depth 24 -geometry 1280x800``
- Use VNC client eg. ``tightVNC`` or ``realVNC`` to login to your VNC server:
 - Enter the appropriate IP and server number eg. ``192.168.6.35:10``
- To kill a server, you have to be logged in to that particular machine and execute:
 - ``vncserver -kill :10``
- To list the current servers you are running: ``vncserver -list``

### /CAD/apps
   
Local scripts, modulefiles, config, etc are maintained in ``/CAD/apps`` which is also maintained on github at ``https://github.com/silicon-vlsi/cad-apps``.

In order to clone it at a different place:
- ``git clone https://github.com/silicon-vlsi/cad-apps``
- Ask sysadmin for email/passwd to clone it.

**NOTE** On a new server when running git for the first time, you need to set:
- ``git config --global user.name "Name" ``
- ``git config --global user.email john@si.com``
- If you get a **Error 403 while accessing URL....** when doing a ``git push``, try:
   - ``git remote set-url origin "https://<github-username/>@github.com/github-username/github-repository-name.git``
   
### ENVIRONMENT VARIABLES

FIXME: This section needs to be updated.
   
- Common environment variables are set in ``/CAD/apps/etc/silicon.csh`` which is soft linked to ``/etc/profile.d/silicon.csh``. **NOTE** This script gets executed twice so you have to make sure you do a conditional statement when *appending* environment variables.
- All tool and project based environment are loaded using the opensource linux software ``module``.
- The the setup files for all the tools and the projects are in ``/CAD/apps/modulefiles``
- For help type ``module``
- To see all the available modules available:
   
```bash
$module avail

Example output:
---------------------------- /CAD/apps/modulefiles -----------------------------
project/cad-analog/1.0            tools/IC/616
project/cad-xfab/xc06m3-18-a0-1.0 tools/INCISIVE/152
tools/ASSURA/41-615               tools/MMSIM/141
tools/EDI/142                     tools/PVS/151
tools/EXT/15-14                   tools/RC/142

```

- To load a particular module eg. ``tools/IC/616``
   
```bash
$module load tools/IC/616
```

- To list loaded modules:
   
```bash
$module list
```

- To see all the settings in a module eg. project/cad-analog/1.0:
```bash
$module show project/cad-analog/1.0
```

- To unload a module
```bash
$module unload project/cad-analog/1.0
```

### BACKUP/Archives

- Currently backups are run weekly (Sunday 4am) and Monthly (Month 1st) from ``VLSI-SERV-001`` to ``VLSI-NAS-001``
- Backup script ``/CAD/apps/bin/rsync2nas`` is run with the weekly and monthly option using ``crontab`` from the ``root`` account. In order to list all the commands in ``root``'s ``crontab``:

```bash
#crontab -l
```
- To add more backup directories, append to script ``/CAD/apps/bin/rsync2nas``
- On the NAS server (``VLSI-NAS-001``), the monthly backups are archived into a tar ball every month using the script ``~/scripts/tar2archive`` in ``admin-vlsi`` user home.
- And ``tar2nas`` is run by a ``crontab`` in the NAS server's ``root`` account. **FIXME** Backup ``~/scripts``.
- Temporary data can be archived in ``VLSI-SRV-002:/home/local/archive``

### FLEXNET LICENSE SERVER (CADENCE/MENTOR)
   
- **Cadence License Documentation** at ``$CDSDOC/license`` or ``/CAD/IC616/doc/license`` [Link-to-PDF](https://www.dropbox.com/s/ou3vda9vpjtnsys/license.pdf)
- **Mentor License Manual** [PDF](https://www.dropbox.com/s/7fii27aj97gow26/mgc_licen.pdf)
- Mentor AppNote MG576233 : Scripts for starting license server [PDF](https://www.dropbox.com/s/x5cnh4ubot5ahpz/AppNote-LicAutoStart.pdf PDF)

**FLEXNET LICENSING COMPONENTS**

All the Cadence and Mentor applications are FlexNet-enabled application that communicates with the license server, a license manager daemon that contacts the client applications and passes the connection to the appropriate vendor daemon that tracks the license status and a files that stores licensing data.

- **FlexNet-Enabled Application Program**-- All the Cadence and Mentor applications eg. Virtuoso, Assura, Pyxis, etc.
- **License Manager Daemon (lmgrd)**-- The lmgrd daemon handles initial contact with the client application programs and passes the connection to the appropriate vendor daemon. The ``lmgrd`` daemon also starts and restarts the vendor daemons. 
 - **NOTE** It's best to run the same version of ``lmgrd`` as the vendor daemon ``mgcld/cdslmd``. Also two different versions of ``lmgrd`` can be run simultaneously for different tools. ``lmgrd`` is in almost all the bin directories of Cadence apps.: Copied the the bin directory from ``/cad/INCISIV102_lnx86/tools/bin`` to ``/CAD/licenseServers/cadence/lmtools-v11-7-0-0``
 - For Mentor Graphics the ``lmgrd`` location is ``/CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl`` **NOTE**: ``*.ixl`` is for 32-bit OS and ``*.aol`` is for 64-bit OS. VLSI-SRV-001 is 32-bit RHEL 6.
- **Vendor Daemon (mgcld/cdslmd)**-- The vendor daemon, ``mgcld/cdslmd``, keeps track of the licenses that are checked out. If the ``mgcld/cdslmd`` process terminates for any reason, all users lose their licenses but usually regain them automatically when ``lmgrd`` restarts ``mgcld/cdslmd``. The vendor daemon for Cadence and Mentor:
 - ``/CAD/licenseServers/cadence/lmtools-v11-7-0-0/bin/cdslmd``
 - ``/CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl/bin/mgcld``
- **License File**-- The license file is a text file where FlexNet stores licensing data. Vendor creates this license file, which contains information about the server and ``mgcld/cdslmd`` and at least one line of data, called the INCREMENT line, for each licensed product. Each INCREMENT line contains an encryption code that is based on data on that line, the host ID of the server(s), and other vendor-supplied data such as expiration date, count, and version. For Mentor this file can be downloaded from the support center. After getting the license file, you have to customize the first two lines:

```bash
#Cadence
SERVER VLSI-SRV-001 98BE9429134A 5280
DAEMON cdslmd /CAD/licenseServers/cadence/lmtools-v11-7-0-0/bin/cdslmd

#Mentor
SERVER VLSI-SRV-001 98BE9429134A 1717
DAEMON mgcld /CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl/bin
```

**IMPORTANT NOTE** Keep the ``cdslmd`` version same as that provided originally with the license file else some applications may fail.

- Current **license files** located at 
 - ``/CAD/licenseServers/mentor/licFiles`` and ``/CAD/licenseServers/cadence/licFiles``
- The current used license files are symbolic linked to:
 - ``/CAD/licenseServers/mentor/license-current.dat`` and ``/CAD/licenseServers/cadence/license-current.dat``

**SCRIPT FOR AUTOMATICALLY STARTING THE LICENSE SERVER**
- For reasons of security, the ``lmgrd`` daemon should not be started as root. For the autostart of the license server script ``cdslic/mgclic``, a username of the same name is created as follows: 
 - ``useradd -u 65535 -g 65534 -d / -s /bin/sh -c "License Server" cdslic`` The account has same group ID as user 'nobody', which limits the acess to system and network resources. Strongly advised not make this a NIS user account.
 - As root change the password to a strong one.
 - Since this is a non-priviledged user, make sure it can access the license file in read mode and the log file in read-write mode.
 - Therefore, change the group of the log file ``/var/tmp/lmgrd.log`` to ``65534`` ie.:
 - ``#chgrp 65534 /var/tmp/lmgrd.log``
- Add the scripts ``cdslic/mgclic`` (Cadence/Mentor) in ``/etc/init.d`` and
- change the permissions to ``rwxr-xr-x``: ``#chmod 755 /etc/init.d/[cdslic/mgclic]``
- ``cd /etc/init.d`` and add the run level info to the system services ``#/sbin/chkconfig --add [cdslic/mgclic]``
- To verify it will run at the desired levels: ``$/sbin/chkconfig --list [cdslic/mgclic]``
- In the script you'll find ``#chkconfig: 345 99 30``. The values represent the run levels (``3,4,5``), the start order (99) and the stop order (30). **DO NOT** remove the commnet (#) from this line.
- NOTE: not only this script starts on boot but also shuts down gracefully during shutdown. You can also start, stop, restart, query, etc. the server with the script. The command syntax is:
 - ``service cdslic {start | stop | restart | status | lmver} `` where:
 - ``start``: starts the server
 - ``stop``: stops the server
 - ``status``: status of the server
 - ``restart``: restarts the server
 - ``lmver``: Version of flexnet

**RELEASING UNUSED LICENSES**

- In order to check-in licenses that are sitting idle for a period of time, a file named in the format ``<vendor-daemon>.opt``, ``cdslmd.opt`` and ``mgcld.opt`` are placed in ``/CAD/licenseServers/cadence`` and ``/CAD/licenseServers/mentor`` for Cadence and Menotr repectively, with the following option:
```bash
# Comment
TIMEOUTALL 3600
```

- This ensures all license features that are capable of TIMEOUT, are checked-in after 3600 secs (1 hour) of idle time.
- A detail description of it can be found at the [Cadence Support](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000nZpZ&pageName=ArticleContent)

**SOME USEFUL COMMANDS NOT IN CDSLIC**

- To remove a license from a user or a zombie process (do a lmstat and get all the info):

```bash
$lmremove <feature> <user> <host> <display>
$lmremove Virtuoso_Schematic_Editor_XL amishra DT-005 :0.0
```

- Other useful commands:

```bash
$lic_error
$lmdiag
$lmhostid
```

**ENVIRONMENT VARIABLES FOR APPLICATION TO ACCESS THE SERVER**

```bash
$setenv LM_LICENSE_FILE 5280@VLSI-SRV-001
$setenv CDS_LIC_LICENSE 5280@VLSI-SRV-001
$set path (/CAD/IC616/tools.lnx86/bin:$path)
```

### SETTING UP PROJECT AREA

FIXME: This section needs to be updated.

- Create the new project entry in ``/CAD/apps/bin/proj.list``
- create directory ``/home/NIS/projects/<PROJNAME>/<REV>/work`` and change ``owner`` and ``group`` to ``srout`` and ``users``.
  - **NOTE**: ``PROJNAME`` and ``REV`` should be in uppercase even though the entry in ``proj.list`` is lowercase.
- Create the ``module`` file and the ``alias`` to match the entry in ``proj.list``
- For Cadence, create ``cds-PROJNAME-REV.lib`` and ``cds-PROJNAME-REV.il`` in ``/CAD/apps/etc``
- Create and populate ``/CAD/apps/<PROJNAME>/<REV>/docs`` with tech documents.


**XFAB XC06 0.6um CMOS Technology**

- Project areas for each user is created as separate directory as:

```bash
/home/NIS/projects/<PROJECT>/<REV>/work/<USER>
```

- Example: for the project ``XC06M3-18``, revision ``A0`` and for the user ``admin``, the project area should be:

```bash
/home/NIS/projects/XC06M3-18/A0/work/admin
```

- For the ``XFab`` technology, the project area is automatically created by a perl script ``/CAD/apps/bin/siproj`` [type ``siproj -h`` for help]]. 
- As an example, to **create** a project area for project ``XC06M3-18``, Revision ``A0`` and the technology option ``xc06m3``:
```bash
FIXME
```
- After the project area is created, in order to ``cd`` to the project area and start ``virtuoso``:
```bash
$cadstart
```
- To simply ``cd`` or ``pushd`` to a project area, type ``cdproj`` or ``pdproj`` respectively.


### SETTING UP A NEW LINUX WORKSTATIONS
   
After loading a OS [Redhat/CentOS] on a new workstation, we need to setup the following:
- **NOTE**: For CentOS, choose **desktop** installation when given various options eg. (server, LAMP, desktop, etc.)
- Setup the Network
- Configure it as a NIS client and
- Mount the NFS mounts (``/CAD, /PDK, /home/NIS, /HOME``)
- Common environment setup
- Create the ``/local`` directory 
- For CentOS: install additional libraries for functional Cadence.

Follow these steps for the above configuration:

**NETWORK SETUP**

- ``#system-config-network`` (You can use GUI from the main drop down menu):
  - First select **``Device Configuration``** and select the appropriate device eg. ``eth1``
  - Static IP address: eg. ``192.168.6.50``
  - Netmask: ``255.255.255.0``
  - Gateway: ``192.168.6.126``
  - Primary DNS: ``8.8.8.8``
  - Secondary DNS: ``8.8.4.4``
  - DNS search path: vlsi.silicon
  - Then select **``DNS Configuration``**:
  - Hostname: example ``dt-026``  
    - **NOTE** In some case the ``hostname`` does not get added to ``/etc/hosts`` resulting in a non-working. Add the ``hostname`` manually to ``/etc/hosts``.
  - Primary DNS: ``8.8.8.8``
  - Secondary DNS: ``8.8.4.4``
  - DNS search path: vlsi.silicon
   
**NIS SETUP**
   
- ``#authconfig-tui``
- Select ``NIS`` and click ``Next`` and set the following:
- Domain: ``NIS_Silicon``
- Server: ``192.168.6.50``
   
**NFS MOUNT SETUP**
   
- First make a copy of the ``/etc/fstab``:``#cp /etc/fstab /etc/fstab.orig``
- Add the following to ``/etc/fstab``:

```bash
192.168.6.50:/home/NIS  /home/NIS    nfs    noatime,rsize=32768,wsize=32768
192.168.6.50:/CAD       /CAD         nfs    noatime,rsize=32768,wsize=32768
192.168.6.50:/PDK       /PDK         nfs    noatime,rsize=32768,wsize=32768
192.168.6.50:/HOME      /HOME        nfs    noatime,rsize=32768,wsize=32768
```

- Create the following directories: ``/CAD, /PDK, /home/NIS``
- Type ``#mount -a`` or ``reboot``

**ENVIRONMENT SETUP**
   
- Common environments are in ``/CAD/apps/etc/silicon.csh``
- Create the following link to load for all users:
  - ``#ln -s /CAD/apps/etc/silicon.csh /etc/profile.d/.``
- Create ``/local`` : Local directory to store all temp data (eg. simulation)
  - ``#mkdir /local``
  - ``#chmod 775 /local``
  - ``#chgrp users /local``
   
**CentOS 6.7/6.10 Specific**
   
- **Hostname not in /etc/hosts**
- Some installations don't seem to have the ``hostname`` in ``/etc/hosts``. One of the problem created by it is: ``spectre`` stops with a ``gethostbyname failed`` error.
- Another problem happens, when the IP Address and ``hostname`` not matches with the IP Adress and ``hostname`` in ``/etc/hosts``. For this the error comes as: ERROR (ADE-3036): "Error encountered during simulation"
- The only way to fix it, it seems, is to manually add the hostname to ``/etc/hosts`` eg. ``192.168.6.57  DT-007``. You can also add the hostname to other entries eg. ``localhost``, etc
- **CYBEROAM LOGIN**
- The following steps require internet connection so make sure syberroam is up and you have logged in with your credential.

- **Environment management tool ``module``** is not installed by default. Install it:
  - ``#yum install environment-modules``
- **Korn shell** is required for Cadence Virtuoso which is not installed.
  - ``#yum install ksh``
- **MISSING LIBS**: 
  - While invoking ``virtuoso``, got an error regarding no ``/lib/ld-linux.so.2``. 
  - Using the command ``$yum provides ld-linux.so.2`` indicated, the package ``glibc-2.12-1.212.el6.i686`` so installed it:
  - ``#yum install glibc.i686`` (Maybe installed in 6.10)
  - ``virtuoso`` also needed ``libXp.so.6`` so installed:
  - ``#yum install libXp.x86_64``
  - **NOTE**: ``$yum provides libXp.so.6`` indicated package ``libXp-1.0.2-2.1.el6.i686`` but ``virtuoso`` returned an error as ``Wrong Classs: ECLASS32``. So installed 
  - ``#yum install libXp.x86_64`` and it worked!!
  - Currently Assura-QRC works only in IC617 setup (see the module file ``project/cad-xfab/xc06m3-qrc``). RCX wasn't running in CentOS because of a missing 32-bit library ``libelf.so.1`` (See [Cadence-Support Link](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000nVSNEA2&pageName=ArticleContent ) ). Tried to find the library and include it in ``LD_LIBRARY_PATH`` but could not, so re-installed the package:
  - ``#yum install elfutils-libelf.i686``

- **MISSIGN FONTS**: On starting ``virtuoso``, following warning about missing fonts
   
```bash
*WARNING* Unable to find font name: "-*-courier-medium-r-*-*-12-*".
*WARNING* Cannot find textFont.  Trying font "fixed".
*WARNING* Unable to find font name: "-*-helvetica-medium-r-*-*-12-*".
*WARNING* Using the text font to present labels.
*WARNING* Unable to find font name: "-*-helvetica-medium-r-*-*-12-*".
*WARNING* Using text font to present error messages.
```
- Load the following font package ``xorg-x11-fonts-ISO8859-1-75dpi``:
 - ``#yum install xorg-x11-fonts-ISO8859-1-75dpi``
 - For detailed troubleshooting of fonts issues, check this [ [link](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000nYq6EAE&pageName=ArticleContent&sq=0050V000006mDk8QAE_2018725791911) ] on support.cadence.com **NOTE** Credentials required.

- **YUM UPDATE**:
- Once the system is ready with all the packages, we need to do a system update:
- ``#yum update``
- **NOTE**: If one or more packages are not installing (eg. firefox..., java...), you can exclude them: ``#yum update --exclude=firefox<start> --exclude=java<star>``

**Checking and Completing CentOS 6.10 Installation Automatically**
   
- All the above steps (after NFS mount) has been automated by a shell script.
- **AFTER** creating the ``/CAD, /PDK, /home/NIS`` mounts and successfully mounting it, you can run the following scripts to complete the rest of the installation:
- ``/CAD/apps/bin/finish-centos610-inst``

### TROUBLESHOOTING

 **SSH KEY ACCESS NOT WORKING**

 - After doing the migration, SSH acces using public key is not working.
 - This [article](https://stackoverflow.com/questions/20688844/sshd-gives-error-could-not-open-authorized-keys-although-permissions-seem-corre) has some detail sabout SELinux problems
 - Enabled the log in ``/etc/ssh/sshd_config`` to ``DEBUG3``
 - Wasn't able to read ``~/.ssh/authorized_keys`` even though all permissions was ok 
 - Disabled SELinux in ``/etc/selinux/config`` and still didn't work
 - removing ``.ssh`` and starting fresh seems to work.
 - Deleted ``/var/log/secure`` and now the log won't get updated. Noticed that the previous log file had dot in the end of the permission. So now copied a old secure file which had the dot and stilll won't update. Found from the web that I need to restart ``rsyslog``, ``sudo service rsyslog restart``
 - **SELINUX NOTE** When `tar`-ing a SELinux filesystem, try the `tar --selinux` option. Do some research on it.
   
* * *

[Nemeth-LinuxSysAdmin-4e]:         https://www.google.co.in/books/edition/UNIX_and_Linux_System_Administration_Han/0SIdBAAAQBAJ?hl=en
[Nemeth-LinuxSysAdmin-5e-2017]:    https://www.google.com/books/edition/UNIX_and_Linux_System_Administration_Han/f7M1DwAAQBAJ?hl=en
