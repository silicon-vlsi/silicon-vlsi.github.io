---
sort: 1
---

# IT 

## USER GUIDE

### Getting Started

- To change your **password** use the NIS command `yppasswd`
- To load environment variable for different tools and projects, the open source tool `module-environment` is used which makes it very modular to __load__ and __unload__ environment variables. Following are some examples:
  - `module av` will **display** all the modules available to load or unload.
  - Some common modules for different CAD tools are:
    - `module load tools/IC/618` **loads** all the necessary environment variables for Cadence Virtuoso 6.18
    - `module load tools/SPECTRE/211` will load the env variables for Spectre simulator.
    - `module load tools/calibre/2021-4-33-16` for Calibre layout verification tool.
    - `module load tools/ASSURA/41-618 tools/PVS/19-14-ISR4` for ASSURA and PVS verification tool.
  - `module list` will **list** all the currently loaded modules.
  - `module show tools/IC/618` will **shows** all the environment variables set in that module `tools/IC/618`
  - `module unload tools/IC/618` will **remove** all the environment related to module `tools/IC/618`
- **Simulation results** should be in the local drive in `/home/local/simulation/<USER>` directory. Storing simulation results in your home or project directory will quickly use up your disk quota.
  - To automatically point all your Cadence simulation to the above directory, add the following command to your `.cdsinit` file in the Cadence work area:
    - `envSetVal("asimenv.startup" "projectDir" 'string strcat("/home/local/simulation/" getShellEnvVar("USER") ))`
  - For Tanner tools, the modulefile sets the env variable `TANNERWINEPREFIX` to `/home/local/simulation/<USER>/.wine-2020-3u3` so the user doesn't have to do anything.

### Creating and Using Project and Training Areas

## ADMIN GUIDE

### Frequently Used Commands

- **NOTE**: Keep this section in sync with /CAD/apps7/bin/LinuxRef.md

- `yum provides libXss.so.1` : To **find** a package which __provides__ a certain library eg. `libXss.so.1`
- `umask 027` will result in files with 640 perms and dirs with 750 perm.
- **Output Redirection:**:
  - **bash**: `unix-cmd > output.log 2> error.log` : redirection of **stdout to output.log** and **stderr to error.log** 
  - **sh**: `unix-cmd > output.log 2>&1` : redirection of **stdout and stderr output.log** 
- `chown -R <owner>:<group> <dir>` : Will recursively change __owner__ and __group__ of files and directories.
- `chmod -R a+rX` : will recursively __append__ read/execute(rX) for all (a) ie. user/group/other directories (X) and append only read for files only. **NOTE** This will not change __dot__ files.
- `chmod -R o-rwX` : will recursively __remove__ read/write/execute(rwX) for __others__.
- `chmod -R g+r` : will recursively __append__ read for __groups__.
- `tar -o ...` : -o option will overwrite the ownership to the one who is untaring right now.
- `find . -type d -name .svn -exec rm -rf {}\;`
- `sudo -u <user> <command>` : Runs the `<command>` as user `<user>`
- `sudo -u <user> -g <group> <command>` : Runs the `<command>` as user `<user>` and the group `<group>` instead of the primary group of the user.
- `sudo sh/csh -c "echo NISDOMAIN=vlsi.silicon.ac.in >> /etc/sysconfig/networks"` : Commands which have breaks in them are passed to a shell else after the first part, the rest will executed as the normal user and not sudoer.
- `sudo xfs_quota -x -c 'report -h' /home/nfs1`
- `echo "whatevever text" | sudo tee -a file.txt` : Will echo text as root
- If you have tar ball with no permission for "other" and the user and group does not exist:
  - `tar -o -xzvf file.tar.gz`
  - `chmod -R o+rX <root-dir>` : will recursivley add read perms for files and r+x for directories, for "others" 
- `yum provides libXss.so.1` : To **find** a package which __provides__ a certain library eg. `libXss.so.1`
- **GIT**
  - `git reset <file>` : undo changes

### User Administration

- All user database is maintained in GoogleSheet (vlsi account) `UserList7-AdvVLSI`
- Open the appropriate tab eg. `permanent`, `VLabs`
- Add, remove or modify the user in the sheet.
  - for modified users, put a `*` in front of the serial number to indicate pending changes.
- Export it as a CSV file (`File -> Download -> csv` eg. `2023-0128-VLabs-GroupUpdate.csv`
- Copy the above file to `/CAD/apps7/users`
- Remove all entries except the required users. **NOTE** DO NOT REMOVE THE HEADER (First 5 lines)
- Run `buseradd` script with the appropriate options:
  - New user: `sudo buseradd -cq -i <file.csv>`
    - Option `q` is for setting the quotas. 
  - Update: `sudo buseradd -g -i 2023-0128-VLabs-GroupUpdate.csv`
  - Remove users: `sudo buseradd -d -i <file.csv>`
- Update NIS: `sudo make -C /var/yp`

### Setting up new CentOS 7 Desktop

**MANUAL INSTALL**
- Start desktop with a CentOS7 USB and and boot press `F12 (or F10)` and choose USB media
- After setting the language, keyboard, etc. set the following
  - Set **static network** and **hostname**
    - IP: `192.168.11.xx`
    - SUBNET: `255.255.255.0`
    - GATEWAY: `192.168.11.254`
    - DNS: `10.3.208.1,8.8.8.8`
    - hostname: `<desktopname>.vlsi.silicon.ac.in`
  - Base Environment **GNOME Desktop** with following addons:
    - GNOME Applications 
    - Legacy X Window System Compatibility
    - Office Suite and Productivity
    - Compatibility Libraries
    - Development Tools
    - Security Tools
    - System Administration Tools
  - Choose the default automatic partition.
- Start installation
- Create user:
  - Set password for `root`
  - Create an **administrative** username `centos`
- **Reboot** and login as `centos`
- Add quota option for `/home` in `/etc/fstab` eg:
  - `/dev/mapper/centos-home   /home   xfs   defaults,pquota   0 0`
- `sudo yum install git`
- `git clone https://github.com/silicon-vlsi/cad-apps7`
- `~/cad-apps7/bin/post-install-centos7.sh all`
- `~/cad-apps7/bin/check-install.sh`
- Reboot
- Update when appropriate: `sudo yum update`

**Auto Install Using Kickstart**

- Install CentOS 7 using a kickstart USB media as detailed below.
- After reboot and accepting EULA, login 
- Change the IP address and hostname: `# nmtui`
- Add hostname to `/etc/hosts`
- Add quota option for `/home` in `/etc/fstab` eg:
  - `/dev/mapper/centos-home   /home   xfs   defaults,pquota   0 0`
- `git clone https://github.com/silicon-vlsi/cad-apps7`
- `~/cad-apps7/bin/post-install-centos7.sh all`
- `~/cad-apps7/bin/check-install.sh`
- Reboot
- Update when appropriate: `sudo yum update`

### Cadence/Mentor Flexnet License Server 

**START AND STOP**

- Cadence:
  - `/CAD/apps7/bin/cdslic start` : Starts the License Server 
  - `/CAD/apps7/bin/cdslic stop` : Stops the License Server 
  - `/CAD/apps7/bin/cdslic status` : Checks the statusi eg. license usage
- Mentor/Siemens:
  - `/CAD/apps7/bin/mgclic start` : Starts the License Server 
  - `/CAD/apps7/bin/mgclic stop` : Stops the License Server 
  - `/CAD/apps7/bin/mgclic status` : Checks the statusi eg. license usage

**INSTALLING A NEW LICENCE FILE**

- Stop the license server.
- Copy the new license file to `/CAD/licenseServers/cadence[mentor]/licFiles`
- Update the symbolic link `/CAD/licenseServers/cadence[mentor]/license-current.dat`
- Edit license file too: 1) add the hostname/IP of the server (**srv02**), 2) path to the daemon (**/CAD/.../cdslmd**) and 3) the daemon port (**PORT=5281/1718**): 

```bash
#Cadence
SERVER srv02 98BE9429134A 5280
DAEMON cdslmd /CAD/licenseServers/cadence/lmtools-v11-7-0-0/bin/cdslmd PORT=5281

#Mentor
SERVER srv02 98BE9429134A 1717
DAEMON mgcld /CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl/bin PORT=1718
```

- Start the license server.


**INSTALLING A NEW FLEXNET SERVER**

- **Documnets/Resources**
  - **Cadence License Documentation** at ``$CDSDOC/license`` or ``/CAD/IC616/doc/license`` [Link-to-PDF](https://www.dropbox.com/s/ou3vda9vpjtnsys/license.pdf)
  - **Mentor License Manual** [PDF](https://www.dropbox.com/s/7fii27aj97gow26/mgc_licen.pdf)
  - Mentor AppNote MG576233 : Scripts for starting license server [PDF](https://www.dropbox.com/s/x5cnh4ubot5ahpz/AppNote-LicAutoStart.pdf PDF)
  - Cadence Support Article on setup and debug of license server [Link](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000sbWnEAI&pageName=ArticleContent)

**Flexnet Licensing Components**

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
SERVER srv02 98BE9429134A 5280
DAEMON cdslmd /CAD/licenseServers/cadence/lmtools-v11-7-0-0/bin/cdslmd PORT=5281

#Mentor
SERVER srv02 98BE9429134A 1717
DAEMON mgcld /CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl/bin PORT=1718
```

**NOTE** The server name (srv02 or IP) should be the same as the hostname.

**NOTE** In CentOS 7 pretty much all ports are shut by default. So typically the DAEMON line doesn't have a port assigned so it picks an available free port but in CentOS 7, you have to specifically assign one using the PORT=<NUM> argument and open that port in the firewall.

```bash
sudo firewall-cmd --add-port=5280/tcp --permanent
sudo firewall-cmd --add-port=5281/tcp --permanent
sudo firewall-cmd --add-port=1717/tcp --permanent
sudo firewall-cmd --add-port=1718/tcp --permanent
sudo firewall-cmd --reload
```

**IMPORTANT NOTE** Keep the ``cdslmd`` version same as that provided originally with the license file else some applications may fail.

- Current **license files** located at 
 - ``/CAD/licenseServers/mentor/licFiles`` and ``/CAD/licenseServers/cadence/licFiles``
- The current used license files are symbolic linked to:
 - ``/CAD/licenseServers/mentor/license-current.dat`` and ``/CAD/licenseServers/cadence/license-current.dat``

**Script for Automatically Starting the License Server**
- For reasons of security, the ``lmgrd`` daemon should not be started as root. For the autostart of the license server script ``cdslic/mgclic``, a username of the same name is created as follows: 
 - ``useradd -u 65535 -g 65534 -d / -s /bin/sh -c "License Server" cdslic`` The account has same group ID as user 'nobody', which limits the acess to system and network resources. Strongly advised not make this a NIS user account.
 - As root change the password to a strong one.
 - Since this is a non-priviledged user, make sure it can access the license file in read mode and the log file in read-write mode.
 - Therefore, change the group of the log file ``/var/tmp/lmgrd.log`` to ``65534`` ie.:
 - ``#chgrp 65534 /var/tmp/lmgrd.log``
 - ``#chown cdslic /var/tmp/lmgrd.log``

**NOTE**: The following method does not apply to CentOS 7.
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

### Setting up a Project

- **CREATE A PROJECT USER**
  - Create an __user__ for each project using the convention of starting letter `p` eg. `pvolta`
  - Change `umask` in `.cshrc` to `027` so files created by `pvolta` cannot be read by __others__.
- **CREATE THE PROJECT DIRECTORY**
  - create the project directory: `/home/nfs1/projects/VOLTA/REV1/work`
  - Change projects owner and group of `VOLTA` directory and underneath to `pvolta`
  - The `work` directory should have `g=swrx,t+` for `pvolta` group so users in `pvolta` group can create project area under this directory but they cannot delete the `work` directory and all the newly created files/directory by project users will have their file groups with the original group of the directory so all users in the group `pvolta` can share files.
    - `# chmod g=swrx,+t work`
    - `s` sets the `setgid` bit for the directory. When set on a directory, the __setgid__ bit causes newly created files within the directory to take on the group ownership of the directory ie. `pvolta` rather than the default/primary group of the user that created it. This makes it easier to share the directory among several users, as long as they belong to the same group. This one is especially useful for shared project directory.
    - `+t` keeps the dir `work` sticky, the filesystem won't allow you to delete or rename it unless you are the owner. Just write permission is not enough. This way users in the 'pvolta' group will not be able to delete it, only the creator.
    - For more on __setgid__ and __sticky__ bits, see Section 5.5 (p-132) in [Nemeth-LinuxSysAdmin-5e-2017]
  - Now you can create the master work area with the user `pvolta` using the `siproj` script.
    - For other users, first include them in the project group eg. `pvolta`
  - Assign __quota__ for the project directory. [See quota section](#quota)
 
- **SETTING UP CONFIG FILES** 
  - Steps to get a project setup so users can use the `siproj` script to setup a project area.
  - Create an entry in `/CAD/apps7/bin/training.list`.
    - `project.list` for projects.
  - Check the script `/CAD/apps/bin/siproj` for any customization needed.
  - Create the `modulefile` in `/CAD/apps7/modulefiles/projects`
  - Create an alias for it in `/CAD/apps7/etc/silicon.csh`
    - `silicon.csh` soft linked in `/etc/profile.d`
  - For **Cadence**:
    - Create a cdsinit file eg. `/CAD/apps7/etc/cdsinit-VOLTA-REV1.il`
    - Create a cds.lib file eg. `/CAD/apps7/etc/cds-VOLTA-REV1.lib`
    - Create dummyLibs (optional) for a tree-like library in LibManager. eg. `/CAD/apps7/etc/dummyLibs/TeslaRev1Libs`


### Users/Groups

- [Redhat Doc on Users/Groups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-managing_users_and_groups)
- Range of `UID/GID`, umask,etc are set in `/etc/login.defs`
- Password aging can be set with `chage`
- The GUI for user management is `Users`
- See [Redhat Doc on Users/Groups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-managing_users_and_groups) for **command-line utilities** for user/group management.
- A good article on [Unix Groups](https://csguide.cs.princeton.edu/account/groups) from Princeton's CS Dept.
- **Shared Directory**: eg `/home/local/simulation`. Created a group called `localsim` which will consists of all users allowed to share the directory.
  - `# chgrp localsim /home/local/simulation`
  - `# chmod g=swrx,+t /home/local/simulation`
    - `s` sets the `setgid` bit for the directory. When set on a directory, the __setgid__ bit causes newly created files within the directory to take on the group ownership of the directory rather than the default/primary group of the user that created it. This makes it easier to share the directory among several users, as long as they belong to the same group. This one is especially useful for shared project directory.
    - `+t` keeps the dir sticky, the filesystem won't allow you to delete or rename it unless you are the owner. Just write permission is not enough. This way users in the 'localsim' group will not be able to delete it, only the creator.
    - For more on __setgid__ and __sticky__ bits, see Section 5.5 (p-132) in [Nemeth-LinuxSysAdmin-5e-2017]


### NIS

- Followed the following two blogs to setup but the client got all broken so need to debug.
  - [NIS Server setup](https://www.server-world.info/en/note?os=CentOS_7&p=nis&f=1)
  - [NIS Client setup](https://www.server-world.info/en/note?os=CentOS_7&p=nis&f=2)

**NIS SERVER ON CENTOS 7**

- `# yum -y install ypserv rpcbind`
- `# ypdomainname vlsi.silicon.ac.in`
- Add `NISDOMAIN=vlsi.silicon.ac.in` to `/etc/sysconfig/network`
- **Ignored** the `/var/yp/securenet` instruction. Was probably creating a problem where I had to restart `rpcbind` everytime there was an update to the server.
- Add the server and the clients' IP address for NIS database to `/etc/hosts`

```bash
192.168.6.50   srv01.vlsi.silicon.ac.in srv01
192.168.6.202  dt042.vlsi.silicon.ac.in dt042
```

- `#systemctl start {rpcbind, ypserv, ypxfrd, yppasswdd}`
- `#systemctl enable {rpcbind, ypserv, ypxfrd, yppasswdd}`
- Upadate NIS database: `#/usr/lib64/yp/ypinit -m`
  - Add the list of NIS servers : `srv01.vlsi.silicon.ac.in` 
  - 'Ctrl+D' to end the list of servers.
- This will build the database in `/var/yp/<DOMAINNAME>`
  - If you have slave servers: run `# ypinit -s srv01` on all slave servers.
- Now when you add an user to the local server `srv01`, update NIS database:
  - `# make -C /var/yp`
- To allow ports in the firewall, add the following to `/etc/sysconfig/network`

```bash
YPSERV_ARGS="-p 944"
YPSERV_ARGS="-p 945"
```

- Add `YPPASSWD_ARGS="--port 946` to `/etc/sysconfig/yppasswdd`
- `# systemctl restart rpcbind ypserv ypxfrd yppasswdd`
- Open the ports in firewall:

```bash
# firewall-cmd --add-service=rpc-bind --permanent
# firewall-cmd --add-port=944/tcp --permanent
# firewall-cmd --add-port=944/udp --permanent
# firewall-cmd --add-port=945/tcp --permanent
# firewall-cmd --add-port=945/udp --permanent
# firewall-cmd --add-port=946/udp --permanent
# firewall-cmd --reload
```

**NIS CLIENT ON CENTOS 7**

- `# yum install ypbind rpcbind`
- `# yum ypdomainname vlsi.silicon.ac.in`
- Add `NISDOMAIN=vlsi.silicon.ac.in` to `/etc/sysconfig/network`
- `# authconfig --enablenis --nisdomain=vlsi.silicon.ac.in --nisserver=srv01.vlsi.silicon.ac.in --update`
  - **Note** If the homedirs are NFS mounted, then no need to use the option `--mkhomedir`
- `# systemctl start rpcbind ypbind`
- `# systemctl enable rpcbind ypbind`
- Type `ypwhich` to see what NIS server is the client binding to.
- To change passwd in the client, use `yppasswd`
- **Note** Ignored the instruction on how to enable automatic creation of homedir for SELinux enabled linux. For NFS mounted homedir, mkhomedir does not work so I don't think this appies to NFS mounted homedirs.


### NFS Share

**Important Files for NFS Configuration**
- ``/etc/exports``: Its a main configuration file of NFS, all exported files and directories are defined in this file at the NFS Server end.
- ``/etc/fstab``: To mount a NFS directory on your system across the reboots, we need to make an entry in /etc/fstab.
- ``/etc/sysconfig/nfs``: Configuration file of NFS to control on which port rpc and other services are listening. **NOTE** In our setup, we just use the default options.

**Configuring and starting a NFS Server on CentOS 7**
- **NOTE** The CentOS 7 installation was done with base installation of __File Server with GUI__ so most needed packages were already installed. 
- Install the necessary packages: `#yum -y install nfs-utils`
- Change owner and group of the NFS share: `#chown nfsnobody:nfsnobody /home/nfs1`
  - This is for security so if there is breach through NFS the user nfsnobody has no shell. 
- Enable NFS port (2049/tcp and 2049/udp) through the **firewall**
  - `# firewall-cmd --permanent --add-port=2049/tcp`
  - `# firewall-cmd --permanent --add-port=2049/udp`
  - `# firewall-cmd --permanent --add-service=nfs`
  - `# firewall-cmd --reload`
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
  - ``async``: This option allows the clients to write to the files before they are written to the disk. It will improve speed but may have problems with two clients writing simulataneosly. See this [post](https://serverfault.com/questions/499174/etc-exports-mount-option#:~:text=exports(5)%20async%20This%20option,storage%20(e.g.%20disc%20drive).) for details.
  - ``no_subtree_check``: This option prevents the subtree checking. When a shared directory is the subdirectory of a larger file system, nfs performs scans of every directory above it, in order to verify its permissions and details. Disabling the subtree check may increase the reliability of NFS, but reduce security.
  - ``no_root_squash``: Root access allowed for mounted directories. **Dangerous!!** **Note** When root access is necessary, enable it temporarily.
  - If the export is through kerberos then you need option `sec=sys:krb5p` **FIXME** Need to research more.

**NFS Client**

- Test mounting the share.. eg. : `#mount -t nfs srv01:/home/nfs1 /home/nfs1`
- If all works add the mounts to `/etc/fstab`:

```bash
srv01:/home/nfs1        /home/nfs1      nfs     noatime,rsize=32768,wsize=32768
srv01:/home/nfs2        /home/nfs2      nfs     noatime,rsize=32768,wsize=32768
```

**Troubleshooting NFS**

- `#mount -v ...` will output debug information
- On the server `# iptables -S | grep 2049` will show if the NFS ports are in the firewall rules.
- `# rpcinfo -p <server/client>` will show all the RPC port info. Check if NFS port is open. 
- If `rpcinfo` shows **no route to host** probably port is not open in the firewall or network routing issues.
- `# route -n` to check the network route.


**Resources**

- [Setting up kerboros aware NFS Server](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/krb-nfs-server)
- [Setting up kerboros aware NFS Client](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/krb-nfs-client)


### Creating a Mirror using rsync

This section will walk you through the steps of mirroring a server directory eg. `srv01:/CAD` in a second file server eg. `srv03:/CAD` to create a mirror of the same. 


**SETUP THE DESTINATION**

- Make sure `rsync` is installed in both the linux servers: `yum install rsync`
- Configure `rsync` __daemon__ by editing `/etc/rsyncd.conf` on the **destination server**:

```bash
# any name you like
[cad]
# destination directory for copy
path = /CAD
# hosts you allow to access
hosts allow = <IP ADDRESS OF SOURCE>
hosts deny = *
list = true
uid = root
gid = root
read only = false
```

- Befor starting the daemon, open the port `873/tcp` and the service `rsynd` :
  - `# firewall-cmd --permanent --add-service=`rsyncd`
  - `# firewall-cmd --permanent --add-port=873/tcp`
  - `# firewall-cmd --reload`
- Start and enable the __daemon__ :
  - `# systemctl start rsyncd`
  - `# systemctl enable rsyncd`

**INITIATE TRANSFER FROM THE SOURCE**

- `# rsync -avz --delete /CAD/   <IPADDR/HOSTNAME DESTINATION>:/CAD`
  - **NOTE** Looks like I can sync to any directory not just the one in __path__ in `/etc/rsyncd.conf`
  - It's important to have the directory end in `/` ie. `/CAD/` instead of `/CAD`. The later will get synced to destination `/CAD/CAD`
- You can include the above in a **crontab** for scheduled syncing.
- For eaxample: To sync everyday at 4AM, the crontab entry will loke like this:
  - `00 04 * * * rsync -avz --delete /CAD/   <IPADDR/HOSTNAME DESTINATION>:/CAD > /var/log/rsync-cad.log 2> /var/log/rsync-cad.err`

**Resources**

- [Rsync : Sync Files/Directories](https://www.server-world.info/en/note?os=CentOS_7&p=rsync)
- [How to Use Rsync to Copy/Sync Files Between Servers](https://www.atlantic.net/vps-hosting/how-to-use-rsync-copy-sync-files-servers/)

### Quota

**SETTING DISK QUOTA ON A XFS FILESYSTEM ON CENTOS 7**

- The instructions are from this [Redhat doc](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsquota)
- XFS quotas are enabled at mount time, with specific mount options. Each mount option can also be specified as `noenforce`; this allows usage reporting without enforcing any limits. Valid quota mount options are:
  - `uquota`/`uqnoenforce`: User quotas
  - `gquota`/`gqnoenforce`: Group quotas
  - `pquota`/`pqnoenforce`: Project quota
- An example of an entry in `/etc/fstab`:
  - `/dev/mapper/nfs-home_NFS /home/nfs1  xfs defaults,uquota,gquota,pquota        0 0`
- To set a block limit for an user (say `500MB` for `user1`):
  - `# xfs_quota -x -c 'limit -u bsoft=400m bhard=500m user1' /home/nfs1`
  - To set a group limit (say `500MB` for the ENTIRE group `eng`), use the above command with the exeception: `-g` and `eng` instead of __-u__ and __user1__ 
- **Setting Project Limits**:
  - Before configuring limits for project-controlled directories, add them first to `/etc/projects`. Project names can be added to `/etc/projectid` to map project IDs to project names. Once a project is added to `/etc/projects`, initialize its project directory using the following command:
    - `# xfs_quota -x -c 'project -s projectname' project_path`
  - Quotas for projects with initialized directories can then be configured, with:
    - `xfs_quota -x -c 'limit -p bsoft=1000m bhard=1200m projectname'
    - Example from the man page:

```bash
 # mount -o prjquota /dev/xvm/var /var
 # echo 42:/var/log >> /etc/projects
 # echo logfiles:42 >> /etc/projid
 # xfs_quota -x -c 'project -s logfiles' /var
 # xfs_quota -x -c 'limit -p bhard=1g logfiles' /var
```

    - Same as above without the need of config file:
  
```bash
# rm -f /etc/projects /etc/projid
# mount -o prjquota /dev/xvm/var /var
# xfs_quota -x -c 'project -s -p /var/log 42' /var
# xfs_quota -x -c 'limit -p bhard=1g 42' /var
```

- **Reporting Quota Limits**:
  - `$ quota username`
  - `# xfs_quota -x -c 'report -h' /home/nfs1`


### VIRTUAL MACHINES

#### Setting up VM in CentOS 7 using KVM

- Useful Resource:
  - [Install KVM Hypervisor on CentOS7](https://www.linuxtechi.com/install-kvm-hypervisor-on-centos-7-and-rhel-7/): Primarily used this as a guide.
  - [KVM on a Headless CentOS 7](https://www.cyberciti.biz/faq/how-to-install-kvm-on-centos-7-rhel-7-headless-server/)
  - [KVM Bridge on CentOS 7](https://tuxfixer.com/install-and-configure-kvm-qemu-on-centos-7-rhel-7-bridge-vhost-network-interface/)
  - [Configuring Storage for QEMU/KVM in CentOS7](https://bashtheshell.com/guide/configuring-lvm-storage-for-qemukvm-vms-using-virt-manager-on-centos-7/#)
  - [Bridge Interface to Access Host network](https://getlabsdone.com/how-to-create-a-bridge-interface-in-centos-redhat-linux/)

- Essentially followed this [great blog](https://www.linuxtechi.com/install-kvm-hypervisor-on-centos-7-and-rhel-7/)
- When installing the CentOS 7, if you choose the Virtualization along with a Display Manager (Gnome, KDE) then you can skip the following:
  - `# yum install qemu-kvm qemu-img virt-manager libvirt libvirt-python libvirt-client virt-install virt-viewer bridge-utils`
  - `# systemctl start libvirtd`
  - `systemctl enable libvirtd`
  - `lsmod | grep kvm` To check if KVM module is loaded or not.
  - `yum install "@X Window System" xorg-x11-xauth xorg-x11-fonts-* xorg-x11-utils -y` if Mimimal install without X.
- Before starting the Virtual Manager, configure the network bridge.
- In this installation our host has multiple ethernet ports so we are going to dedicate one of the ports to the Virtual machine so the Virtual Machine looks as part of the host network instead of the default VMs behind a virtual NAT. This also does a nice load balancing on the ports. This part took some searching to get it working. Check this [Blog](https://getlabsdone.com/how-to-create-a-bridge-interface-in-centos-redhat-linux/).
- Easiest way to configure the new one is to copy a working config and change the IP address (if static) and UUID.
  - `cd /etc/sysconfig/network-scripts`
  - `cp ifcfg-em2 ~/baks/ifcfg-em2-orig` : backup the original file.
    - **NOTE** Do not copy backups in `/etc/sysconfig/network-scripts` since the Network Manager reads all the files and will make a mess of the network.
  - `cp ifcfg-em1 ifcfg-em2`
  - Update the unique params:

```bash
TYPE=Ethernet
BOOTPROTO=static
DEVICE=em2
ONBOOT=yes
UUID="e1382762-ad1f-4ca3-9981-09489cbb948a"
NAME=em2
BRIDGE=br0
IPADDR=192.168.6.30
NETMASK=255.255.255.0
GATEWAY=192.168.6.254
DNS1=10.3.208.1
DNS2=8.8.8.8
```

- Create the bridge interface `/etc/sysconfig/network-scripts/ifcfg-br0`

```bash
TYPE=Bridge
BOOTPROTO=static
DEVICE=br0
ONBOOT=yes
IPADDR=192.168.6.30
NETMASK=255.255.255.0
GATEWAY=192.168.6.254
DNS1=10.3.208.1
DNS2=8.8.8.8
```

- `sudo systemctl restart NetworkManager`
- Check the interfaces `ip a`:

```bash
3: em2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master br0 state UP group default qlen 1000
    link/ether 00:00:00:00:62:3a brd ff:ff:ff:ff:ff:ff
6: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
    inet 192.168.6.30/24 brd 192.168.6.255 scope global noprefixroute br0
       valid_lft forever preferred_lft forever
```

- `sudo virt-namanger` : Start the Virtual Manager.
- Choose the ISO image to start isntallation.
- Choose Memory and CPUs.
- For storage, A LVM partition in the host was used which was mounted on say `/home/vm1`
  - Select "enable storage for this virtual machine"
  - Select "Select or create custom storage" and click `Manage`
  - "Choose Storage Volume" dialog box will pop up. Click the "+" sign.
  - Give a name say `vm1-pool` and choos type `dir:Filesystem Directory` and click `Forward`
  - Browse to the mounted directory `/home/vm1` and click `Finish`
  - Now you should see the volume `vm2-pool` in the Volume manager.
  - Select the volume and click the "+" sign
  - Give a name and select format (default "qcow2") and choose the size. 
  - Now choose the volume and start the installation.
- After system reboot, from the VM console click "Show Virtual Hardware Detail" (A light bulb sign)
  - Click the "NIC" hardware 
  - Choose `Bridge br0` Network source.
  - Device Model : `virtio`
- After installation the network is going to be in DHCP mode, use `nmtui` to change it static and provide a inique IP address eg `192.168.6.31`.
- Follow the steps on creating a new desktop to complete the setup.


#### Cloning a Virtual Machine

- Easiest way to create a quick clone is to use the `virt-manager`, right-click on the VM (that is shutdown) and click "Clone".
- Use defaults to create an exact clone but with the a seprate hardisk on the same directory as one used above.
- Power up the clone first and change the **Hostname** and **IP Address**. Everything else should run as the parent VM. 

### X-Server
Tags: #xserver #vncserver

**Useful Links**

- [TigerVNC doc@Redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-tigervnc)

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
   
   - Check the firewall running status: `$sudo firewall-cmd --state`
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
   

### Remote Access

**PPTP VPN CLIENT ON CentOS-7**

- [See this site](https://zlthinker.github.io/Setup-VPN-on-CentOS) for step-by-step instruction on how to setup a PPTP VPN connection from CentOS 7.

### EDA Tools

#### CADENCE

**IC 618 ON CENTOS 7**

- After installation run the patch test:
  - `<INSTALL-DIR>/tools.lnx86/bin/checkSysConf IC6.1.8`
  - Required packages: `glibc`, `elfutils-libelf`, `ksh`, `mesa-libGL`, `mesa-libGLU`, `motif`, `libXp`, `libpng`, `libjpeg-turbo`, `expat`, `glibc-devel`, `gdb`, `xorg-x11-fonts-misc`, `xorg-x11-fonts-ISO8859-1-75dpi7.5`, `redhat-lsb`, `libXscrnSaver`, `apr`, `apr-util`, `compat-db47`, `xorg-x11-server-Xvfb`, `mesa-dri-drivers`, `openssl-devel`

**MMSIM ON CENTOS 7**

- **NOTE** MMSIM is no longer supported by Cadence. It's SPECTRE. But we don't have the license for it.
- Used the previous installation MMSIM15.1 and seems to work without any extra patches/pkgs.

**SPECTRE ON CENTOS 7**

- **NOTE** We DO NOT HAVE licenses for SPECTRE right now. And Cadence doesn't support MMSIM anymore. So need to use the old installation.
- Installed Spectre (21.1) using iScape
- Read the Relase Notes from iScape.
- Run `checkSysConf` to check the OS, packages, patches, etc needed to run Spectre

#### iverilog and gtkwave 

- **NOTE** The following instruction is for installing `iverilog` and `gtkwave` from the CentOS 7 EPEL repo. So it needs to be installed in each desktop seprately.
- Add EPEL (if not already) repo to the installer: `sudo yum install epel-release`
- `sudo yum install iverilog`
- `sudo yum install gtkwave`
- To compile simple verilog module and it's testbench: say `mydut.v` and `tb_mydut.v`
  - `iverilog -o tb_mydut.vvp mydut.v tb_mydut.v` : Compile the verilog codes and create an output `tb_mydut.vvp`
  - `vvp tb_mydut.vvp` : Convert the compiled output to a VCD format for GTKWave.
  - `gtkwave dump.vcd` : Note: the filename `dump.vcd` is assumed to be in `tb_mydut.v`

### Creating a Kickstart USB Boot Media

- [Automatic Install Doc from Redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-simple-install-kickstart)
  - [Kickstart Syntax](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-kickstart-syntax)
- After a manual insallation, **Anaconda** records the steps in `/root/anaconda-ks.cfg`
- Download the CentOS iso to say `/root/`
- `# mount -o loop /root/centos7x64.iso /mnt/`
- Create a working directory and copy the DVD content to it. For example:

```bash
# mkdir /root/centos-install/
# shopt -s dotglob
# cp -avRf /mnt/* /root/centos-install/
```

- `# umount /mnt/`
- Edit the **kickstart** file `anaconda-ks-desktop.cfg` which contains all installation and post-install confguration, mainly:
  - Set the network (**NOTE** the IP address and hostname is set to a temporary one)
  - Add all extra packages in the `%package` section
  - Add post-installation script:
    - Append `/etc/hosts`
    - Create mount points for NFS mounts
    - Append `/etc/fstab` with NFS mounts
    - ln -s /CAD/apps7/etc/silicon.csh /etc/profile.d/.
    - Setup NIS client
    - make local directory
- `$ksvalidator anaconda-ks-desktop.cfg`
- `# cp /root/anakonda-ks.cfg /root/centos-install/`
- Replace __white space__ with `\x20` : 

```bash
# isoinfo -d -i rhel-server-7.3-x86_64-dvd.iso |\
  grep "Volume id" |\
  sed -e 's/Volume id: //' -e 's/ /\\x20/g
```

- Add a new menu entry to the boot `/root/centos-install/isolinux/isolinux.cfg` file that uses the Kickstart file. The LABEL is the output from the previous command. For example:

```bash
label kickstart
menu label ^Kickstart Installation of CentOS 7
kernel vmlinuz

append initrd=initrd.img inst.stage2=hd:LABEL=CentOS\x207\x20x86_64 \
        inst.ks=hd:LABEL=CentOS\x207\x20x86_64:/anaconda-ks.cfg
```

- For USB UEFI boot, edit the grub.cfg
- Mount the volume: `# mount /root/centos-install/images/efiboot.img /mnt/`
- Add a new menu entry to `/mnt/EFI/BOOT/grub.cfg`

```bash
menuentry 'Kickstart Installation of CentOS 7' \
          --class fedora --class gnu-linux --class gnu --class os {
        linuxefi /images/pxeboot/vmlinuz inst.stage2=hd:LABEL=CentOS\x207\x20x86_64 \
           inst.ks=hd:LABEL=CentOS\x207\x20x86_64:/anaconda-ks.cfg
        initrdefi /images/pxeboot/initrd.img
}
```

- `umount /mnt`
- Create the ISO **NOTE**: The volume Id has the original spaces instead of of \x20

```bash
# mkisofs -untranslated-filenames -volid "CentOS 7 x86_64" \
  -J -joliet-long -rational-rock -translation-table -input-charset utf-8 \
  -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot \
  -boot-load-size 4 -boot-info-table -eltorito-alt-boot \
  -e images/efiboot.img -no-emul-boot -o /root/centos-ks.iso \
  -graft-points /root/centos-install/
```

- Make it bootable: `# isohybrid --uefi centos-ks.iso`
- Make a bootable USB: `# dd if=centos-ks.iso of=/dev/sdb bs=512k`
  - **NOTE** WRITE TO PARENT DEVICE eg. **/dev/sdb** and NOT __/dev/sdb1__
 

## LAB IT INFO

### NETWORKING

**IP ASSIGNMENTS**

- The Advanced VLSI Lab now is under a new VLAN `192.168.11.0/24` with the following broad assignment:
  - Training Lab: `192.168.11.1-30`
  - Advanced VLSI Lab: `192.168.11.31-70`
  - DHCP: `192.168.11.71-220`
  - Servers: `192.168.11.221-153` : 
    - SRV01: em1: `11.221` (File Server), em2: `11.222` (License Server)
    - SRV02: em1: `11.229`, em2<-br0: `11.230`, em3: `--`, em4: `--`
      - vm01: eth0<-br0 : `11.231`
      - vm02: eth0<-br0 : `11.232`
    - SRV03: en02: `--`, en03: `11.237`, en04: `--`, en05: `--`
  - Gateway: `192.168.11.254`
  - DNS: `10.3.208.1`, `8.8.8.8`

- **Domain Name**: `vlsi.silicon.ac.in`
  - `srv01.vlsi.silicon.ac.in` : 192.168.11.221

### STORAGE

**PARTIONING**

**srv01.vlsi.silicon.ac.in**

This is the primary NFS file server. `/CAD` and `/PDK` are mounted in the NeumannLab (training). VoltaLab uses srv03 to mount `/CAD` and `/PDK`

| **Mount** | **Size** | **Purpose** |
| ``swap`` | 8G | 0.5xRAM-size [Recommendation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-swapspace#tb-recommended-system-swap-space)|
| ``/boot`` | 1.5G | Boot files |
| ``/boot/efi`` | 0.5G | EFI boot files |
| ``/(root)`` | 125G | CentOS 7 installation files |
| ``/home`` | 25G | Local home dir |
| ``/var`` | 25G | log,etc |
| ``/home/local`` | 400G | local mount (sims, etc) |
| ``/home/nfs1`` | 250G | NFS mount for homes/projects  |
| ``/home/nfs2`` | 100G | NFS mount for trainings |
| ``/CAD`` | 650G | NFS mount for NeumannLab |
| ``/PDK`` | 270G | PDK mount for NeumannLab |


**srv02.vlsi.silicon.ac.in**

This is a computer server with **20 Xeon Cores** and **128GB RAM**. This also serves the Cadence and Mentor License Servers.
 
| **Mount** | **Size** | **Purpose** |
| ``swap`` | 16G | >4G [Recommendation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-swapspace#tb-recommended-system-swap-space)|
| ``/boot`` | 1.5G | Boot files |
| ``/boot/efi`` | 0.5G | EFI boot files |
| ``/(root)`` | 125G | CentOS 7 installation files |
| ``/home`` | 100G | Local home dir |
| ``/var`` | 50G | log,etc |
| ``/home/local`` | 500G | local mount (sims, etc) |
| ``/home/virt1`` | 250G | Used for VirtualMachine VM1-srv02|
| ``/home/virt2`` | 250G | For Virtual Machines |
| ``/home/virt3`` | 250G | For Virtual Machines |
| ``/home/virt4`` | 250G | For Virtual Machines |


**srv03.vlsi.silicon.ac.in**

This is the first file server of VLSI Lab. It's used as a shadow server which mirrors srv01. Currently only /CAD and /PDK are mirrored (rsynced)

| **Mount** | **Size** | **Purpose** |
| ``swap`` | 8G | 0.5xRAM-size [Recommendation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-swapspace#tb-recommended-system-swap-space)|
| ``/boot`` | 1.5G | Boot files |
| ``/boot/efi`` | 0.5G | EFI boot files |
| ``/(root)`` | 125G | CentOS 7 installation files |
| ``/home`` | 25G | Local home dir |
| ``/var`` | 25G | log,etc |
| ``/home/local`` | 200G | local mount (sims, etc) |
| ``/pdk`` | 100G | Mirrors /PDK from srv01  |
| ``/cad`` | 400G | Mirrors /CAD from srv01 |
| ``/home/nfs3`` | 100G | /CAD2 in clients for digital tools|
| ``/home/nfs4`` | 100G | reserved for future use |


## LINUX KNOWLEDGEBASE

- [RHEL 7 Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7)
  - **Deployment**: [Installation Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide)
    - [A Simple guide to Automated Installation using Kickstart File](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-simple-install-kickstart)
    - [Kickstart command reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-kickstart-syntax)
  - **System Administration**:
    - | [**SysAdmin Guide**](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide) | [User/Groups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-managing_users_and_groups#s1-users-groups-introduction) |
    - [Common Admin commands](https://access.redhat.com/articles/1189123)
    - [Using cockpit to manage CentOS/RHEL servers](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/managing_systems_using_the_rhel_7_web_console)
    - [Networking Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
    - [Kernel Admin Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/kernel_administration_guide)
    - [Performance Tuning](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/performance_tuning_guide)
    - [Resource Management](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/resource_management_guide)
  - | **Security** | [Security Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide) | [SELinux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide) |
  - | **Storage** | [Storage Admin Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide) | [LVM Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration) |
 
- [CentOS 7 Release Notes](https://wiki.centos.org/action/show/Manuals/ReleaseNotes/CentOS7.2009?action=show&redirect=Manuals%2FReleaseNotes%2FCentOS7)
- [Fedora Documention](https://docs.fedoraproject.org/en-US/Fedora/19/html/Installation_Guide/index.html): Release 18/19 are closest to CentOS/RHEL 7

  

### MONITOR AND CONFIGURATION
- Installing and using the `authconfig` GUI (**NOTE** `authconfig-tui` is deprecated)
  - `# yum install authconfig-gtk`
  - Launch it: `#system-config-authentication`  **NOTE** command takes effect after quiting GUI.
  - [Redhat documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system-level_authentication_guide/authconfig-install)
- Installing cockpit on CentOS 7:

```bash
# yum install cockpit
# systemctl enable --now cockpit.socket
# firewall-cmd --permanent --zone=public --add-service=cockpit
# firewall-cmd --reload
```

- Access the cockpit from web browser using the URL: `https://<SERVER-IP>:9090`
- Resources:
  -[Intro to Cockpit -- redhat](https://www.redhat.com/sysadmin/intro-cockpit)
  -[Getting started -- redhat 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/getting_started_with_cockpit/index)


### FREE IPA
   
[FreeIPA](https://www.freeipa.org) is an integrated Identity and Authentication solution for Linux/UNIX networked environments combining Linux (Fedora), 389 Directory Server, MIT Kerberos, NTP, DNS, Dogtag (Certificate System). It consists of a web interface and command-line administration tools. A FreeIPA server provides centralized authentication, authorization and account information by storing data about user, groups, hosts and other objects necessary to manage the security aspects of a network of computers.

```note
Home directories cannot be created automatically on NFS mounts when using IPA. This was the major reason for not implementing IPA.
```
   
**FREE-IPA SERVER INSTALLATION ON CENTOS 7**

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
     - If the LDAP (ipa) users home directories are customed ie. not in `/home`. For example: `/home/nfs1` then apply the correct SELinux context and permissions from the `/home` directory to the home directory that is created on the local system eg. `/home/nfs1`: `$ sudo semanage fcontext -a -e /home /home/nfs1`
     - Do the same thing for any other custom home directories.
     - Install, if not already, the `oddjob-mkhomedir` package on the system which provides the `pam_oddjob_mkhomedir.so` library, which the `authconfig` command uses to create home directories. `The pam_oddjob_mkhomedir.so` library, unlike the default `pam_mkhomedir.so` library, can create SELinux labels. The `authconfig` command automatically uses the `pam_oddjob_mkhomedir.so` library if it is available. Otherwise, it will default to using `pam_mkhomedir.so`.
     - Make sure the `oddjobd` service is running: `# systemctl status oddjobd`
     - During the `ipa-server` installation, `ipa-client` is also installed by default without the option `--enablemkhomedir` which is needed to for first login in the server which hosts the home directories so for other clients who mount this directory, they don't need the option. Run the `authconfig` command:

```bash
   # authconfig --enablemkhomedir --update
```
   
   - Check [this](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system-level_authentication_guide/authconfig-homedirs) man page on redhat.com for custom home dir details.
   - [Setting up User Home Directories in IPA -- redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/users#home-directories)
     - **NOTE** in the above document: "Auto-creating home directories for new users on an NFS share is **not supported**"
   
   
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
   - **NOTE** During the install the DNS lookup failed. Changing `/etc/resolv.conf` gets overwritten at boot. Must be a master file that sets. Just make sure that DNS lookup in `/etc/nsswitch.conf` the first nameserver is the IPA server.
 
   **Resources**
   - **automount**
     - **NOTE** Could not get automount to work. Tried mounting NFS using Kerberos but it still didn't.
     - [Setting up User Home directories -- redhat.com](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/users#home-directories)
     - [Using automount -- redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/automount)

   - **Server** 
     - [Quick Start Guide -- freeipa](https://www.freeipa.org/page/Quick_Start_Guide)
       - [Deployment Recommendations](https://www.freeipa.org/page/Deployment_Recommendations)
     - [How to Install and Configure FreeIPA on CentOS 7 Server -- linuxtechi](https://www.linuxtechi.com/install-configure-freeipa-centos-7-server/)
     - [How To Install FreeIPA Server on CentOS 7 -- computingforgeeks](https://computingforgeeks.com/install-freeipa-server-centos-7/)
   - **Client**
     - [Install & configure FreeIPA Server & Client on RHEL/CentOS 7 -- golinuxcloud](https://www.golinuxcloud.com/install-freeipa-server-centos-7/)
     - [How To Configure a FreeIPA Client on CentOS 7 -- digital ocean tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-a-freeipa-client-on-centos-7)
     - [How to Install FreeIPA Client on CentOS 7 -- howtoforge](https://www.howtoforge.com/how-to-install-freeipa-client-on-centos-7/)
   - [FreeIPA Guide -- Fedora 18/19 (CentOS6/7)](https://docs.fedoraproject.org/en-US/Fedora/18/html/FreeIPA_Guide/index.html)
   - [Nemeth-LinuxSysAdmin-5e-2017] : Ch-8/p243 User Mgmt, p-580 LDAP
   
   
   
### SELINUX
   
[Security-Enhanced Linux (SELinux)] is a security architecture for Linux systems that allows administrators to have more control over who can access the system.
   
   -  You can tell what your system is supposed to be running at by looking at the `/etc/sysconfig/selinux` file.
     - Default option mode is `enforcing` and policy is `targeted`
   - OR you can use the command `sudo setatus`
   - The mode can be changed in `/etc/selinux/config` eg. `enforced, permissive, disabled`

```note
 When switching from **Disabled** to either **Permissive** or **Enforcing** mode, it is highly recommended that the system be rebooted and the filesystem relabeled(?).  
```

   - Some useful SELinux commands
     - `$ ls -Z file1`
       - To view the SELinux context of the file in the following format: __user:role:type:level__ 
       - **SELinux user** is a SELinux specific identity using SELinux policy that is mapped to an user to inherit the policy. 
       - The command `# semanage login -l` lists all the users with their assigned SELinux user, the MLS/MCS security level and allowed services.
       - **role** is part of SELinux Role-Based Access COntrol (RBAC) security model.
       - **type** is attribute of Type enforcement.
       - **level** is an attribute of MLS/MCS security level.
     - `tar --selinux ...` to preserve the SELinux properties. **FIXME** Need to research more on this one...
     - 
   - Resources:
     - [SELinux wiki.centos.org](https://wiki.centos.org/HowTos/SELinux#SELinux_Modes)
     - [What is SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)


### RAID/PARTIONING

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


   
- `# /CAD/cadence/SPECTRE211/tools.lnx86/bin/checkSysConf SPECTRE21.1`
  - Install all the missing packages 
  - **Note** Spectre requires a 32-bit (i686) `glibc` along with the 64-bit.
  - Install it with explicit architecture: `#sudo yum install glibc.i686`  


### LXLE VM USING DOCKER

- Check this [PDF](Files/LXLE-Docker-Setup-Guide.pdf)


## OLD WIKI 

### COMPUTING INFRASTRUCTURE
![Computing Infra](IT-infra.png) 

| **Name** | **IP Address** | **Hardware** | **OS** | **Location** | **Purpose** |
| VLSI-SRV-001 | 192.168.6.50 | Xeon/4C/2.5GHz/16Gb | Redhat 6 | Server Room | File Server |
| VLSI-SRV-002 | 192.168.6.35 | Xeon/20C/40T/2+GHz/64G | CentOS-6.7 | Server Room | Computing Server |
| VLSI-NAS1 | 192.168.5.11 | RaspberryPi-3-B+ 1Gb | OpenMediaVault | Server Room | Archive/Backup Server |
| DT-001->030 | 192.168.51->80 | <FIXME> | Redhat 6 / CentOS 6.10 | Adv VLSI Lab | 30 Workstations |

### STORAGE


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
- Cadence Support Article on setup and debug of license server [Link](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000sbWnEAI&pageName=ArticleContent)

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
SERVER srv02 98BE9429134A 5280
DAEMON cdslmd /CAD/licenseServers/cadence/lmtools-v11-7-0-0/bin/cdslmd PORT=5281

#Mentor
SERVER srv02 98BE9429134A 1717
DAEMON mgcld /CAD/licenseServers/mentor/mgls_v9-16_5-1-0.ixl/bin PORT=1718
```

**NOTE** In CentOS 7 pretty much all ports are shut by default. So typically the DAEMON line doesn't have a port assigned so it picks an available free port but in CentOS 7, you have to specifically assign one using the PORT=<NUM> argument and open that port in the firewall.

```bash
sudo firewall-cmd --add-port=5280/tcp --permanent
sudo firewall-cmd --add-port=5281/tcp --permanent
sudo firewall-cmd --add-port=1717/tcp --permanent
sudo firewall-cmd --add-port=1718/tcp --permanent
sudo firewall-cmd --reload
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
