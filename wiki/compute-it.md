---
sort: 1
---

# COMPUTING & IT 

## Cloud Computing

### Setting Up a Linux VM

This section details the steps to setup a __Ubuntu Linode VM__ following the this [guide](https://www.linode.com/docs/guides/set-up-and-secure/).

- Use the cloud manager to create and view the linux instance.
- Before accessing the instance through **PuTTy**, access the instance using the _root_ login via the **web console**. **NOTE** Most cloud services creates an admin account for access which we will create manually in subsequent steps.
- Perform **system updates**: `apt update && apt upgrade`
- Set **local timezone**: `timedatectl set-timezone 'Asia/Calcutta'`
  - To view all timezones: `timedatectl list-timezones`
- Set **hostname**: `hostnamectl set-hostname <hostname>`
- Customize **prompt** in `.bashrc`: `PS1="\u@\h[\W]\$ "`
  - `\u`:username, `\h`:hostname, `\W`: working directory
- Add some essential aliases to .bashrc:

```bash
alias date='date +%D'
alias h='history'
alias vimr='vim -R'
alias rm='rm -i'
```

- Add the above customization to `/etc/skel/.bashrc` for new users.
- Create an **admin/sudo** account:
  - Create the new user: `adduser <admin-username>`
  - Add the new user to the sudo group: `adduser <admin-username> sudo`
  - For other distros eg. CentOS or even Ubuntu:
    - `useradd <adminuser> && passwd <adminuser>`
    - `usermod -aG wheel <adminuser>`
    - Make sure the wheel group is uncommented in `/etc/skel` using the command `#visudo`
      - `%wheel ALL=(ALL) ALL`  **NOTE** `%` is NOT a comment.
- **Harden SSH Access** by adding authentication via private/public key pair and disable password access.
  - For **PuTTy**, use **PuTTygen** to generate private/public key pair.
  - use 4096-bit RSA or ECDSA to generate the key pair.
  - Save the private key in safe location and add it to the PuTTy session: `Connection -> SSH -> Auth -> Private key file for authentication`
  - Add the public key to the Linux VM instance: `~/.ssh/authorized_keys`
  - Andd now when loging in for that particular user, you will not require to use the password.
- **SSH Daemon Options** in `/etc/ssh/sshd_config`:
  - Disable _root_ login via SSH: `PermitRootLogin no`
  - Disable _password auth_: `PasswordAuthentication no`
  - If using only IPv4 then: `AddressFamily inet`
  - _Restart_ ssh daemon: `sudo systemctl restart sshd`
- Use **Fail2Ban** to avoid malicious attack through the SSH port and other ports too. **FIXME** Use the following [Linode Tutorial](https://www.linode.com/docs/guides/using-fail2ban-to-secure-your-server-a-tutorial/) to install and configure fail2ban.
- **Configure Firewall**. The default application in Ubuntu `ufw` (Uncomplicated Firewall) is disabled. Follow the [Linode Tutorial](https://www.linode.com/docs/guides/configure-firewall-with-ufw/) to install and setup the firewall. Basic setup steps:
  - Allow SSH connections: `sudo ufw allow ssh`
  - Use the default rules:
    - `sudo ufw default allow outgoing`
    - `sudo ufw default deny incoming`
  - Enable it: `sudo ufw enable`
  - Check the status: `sudo ufw status`

- **Install and Configure Dropbox**
  - `sudo apt-get install dropbox`
  - Login to the user you want to use Dropbox in.
  - `dropbox update`
  - `dropbox start`
    - _First Time_: `dropbox status` will show you a link that you browse to enter the Dropbox account credential that you want to link to.
  - Make sure everytime the VM is rebooted, you start dropbox.
  - _NOTE_ If your distro's repo does not have the packages, Check this Dropbox Links: [Installs](https://help.dropbox.com/installs), [On Linux](https://help.dropbox.com/installs/linux-commands#add).

- **Additional Packages**
  - To build pacakages from source install the essentials:
    - `sudo apt instal build-essential`
    - If you need the kernel headers: `sudo apt install linux-headers-'uname -r'`

## Networking

### PPTP VPN client in Linux (CentOS7)

**SETUP**

- Followed this [blog](https://zlthinker.github.io/Setup-VPN-on-CentOS) to setup the VPN
- Install PPTP: `sudo yum install pptp pptp-setup`
- Configuration: `sudo pptpsetup –create bmt-229 –server [server address] –username [username] –password [pwd] –encrypt`
- This command will create a file named `bmt-229` under `/etc/ppp/peers/` with server info written inside.
- This command will also write your username and password into `/etc/ppp/chap-secrets`
- Register the ppp_mppe kernel module: `sudo modprobe ppp_mppe`
- Register the nf_conntrack_pptp kernel module: `sudo modprobe nf_conntrack_pptp`

**USER GUIDE**
    
- Connect to VPN PPTP: `sudo pppd call config`
- It will establish PPTP VPN connection. You can type command `ip a | grep ppp` to find the connection name (e.g. `ppp0`). No return indicates connection failure.
- If any error, you can look into `/var/log/messages` for log info
- Check IP routing table info: `route -n`
- Add Network Segment to current connection: 
  - `route add -net 192.168.11.0 netmask 255.255.255.0 dev ppp0`
- You can now ping the destination to check the access
- Disconnect the VPN: `sudo killall pppd`


## WebSite/Wiki

### Jekyll: Static Page on GitHub

[#jekyll](https://jekyllrb.com) | #github | [#jekyll-rtd-theme](https://github.com/rundocs/jekyll-rtd-theme) | #website | #static | [#jekyll-rtd-userguide](https://jekyll-themes.com/jekyll-rtd/) 

This section shows you how to create a static web page using [Jekyll](https://jekyllrb.com) (and a Jekyll theme) and host it on github.

**PREPARING LINUX FOR JEKYLL**
  * First we need install the prereqs on a Linux workstation. Following is for the `Ubuntu` on `AWS`:
  * `sudo apt-get install ruby-full build-essential zlib1g-dev`
  * In order to load `gem` locally, add the following in `.bashrc`
    * `export GEM_HOME=$HOME/gems`
    * `export PATH=$HOME/gems/bin:$PATH`
  * `gem install jekyll bundler`

**CONFIGURING JEKYLL**
  * Site-wise configuration are done using `_config.yml`
  * See https://jekyll-rtd-theme.rundocs.io/ for config options.
  * **IMPORTANT** Option `baseurl` when testing a site that doesn't sit at the root of the server domain. See this [blog](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/#:~:text=Set%20baseurl%20in%20your%20_config,baseurl%20to%20in%20your%20_config) for more detail on it.
  * Someone changed this to `/silicon-vlsi.github.io` and all urls had duplicate domain eg `https://silicon-vlsi.github.io/silicon-vlsi.github.io/content/projects.html` and thus breaking the links.
  * Removed the `baseurl` and `url` as well since hosting on github automatiacally takes care of it. I think. It works so far.

**USING A JEKYLL TEMPLATE IN GITHUB**
  * Login to your github account eg. `silicon-vlsi`
  * Navigate to the template repo (eg. [#jekyll-rtd-theme](https://github.com/rundocs/jekyll-rtd-theme) and click `Fork`
  * Rename (from the repo's settings) the copied repo to the following format:
    * `<username>.github.io`
    * eg. `silicon-vlsi.github.io`
  * Give it few minutes to publish it and browse to `http://silicon-vlsi.github.io` to see the website!

**USING JEKYLL TO MAINTAIN THE SITE**
  * Clone the repo to your prepared Linux workstation:
    * `git clone https://github.com/silcion-vlsi/silicon-vlsi.github.io`
  * Change directory `cd` to `silicon-vlsi.github.io` and edit `_config.yml` change the info.
  * For the first time after clone, to get the dependencies:`bundle install`
    * `bundle update` FIXME Document this
  * Build the site again after the changes:`bundle exec jekyll build`
  * `git commit --all [--allow-empty] -m "comment"` FIXME: Document when we need `--allow-empty`
  * `git push`

**CONTENT MANAGEMENT**

The directory structure (USR tag indicates changes made by the user and SYS typically should be left untouched and synced with the original repo):

```bash
.
├── README.md              : USR: Content for the landing page
├── _config.yml            : USR: Site-wide configuration
├── _includes              : SYS: All includes: common codes, etc
├── _layouts               : SYS: site layout
├── _sass                  : SYS: ??
├── _site                  : SYS: Compiled html site here
├── assets                 : SYS: CSS themes etc.
├── content                : USR: Main site content goes here.
│   ├── README.md
│   ├── Resources
│   ├── people.md
│   ├── projects.md
│   └── training.md
└── wiki                   : USR: The second content page
    ├── README.md
    ├── doc1
    ├── doc2
    └── quickref.md

```

**SYNCING THE LOCAL FORK WITH ORIGINAL UPSTREAM REPO**
FIXME Refer a proper documentation for this and put some more detail in this documentation. 

  * Related github docs: [Config a remote for fork](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/configuring-a-remote-for-a-fork), [Syncing a fork](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)
  * **Onetime** config remote upstream repo with the fork:
    * List the current configured remote repository for your fork.`git remote -v`
    * Specify the remote upstream repository that will be synced with the fork:`git remote add upstream  https://github.com/rundocs/jekyll-rtd-theme.git`
    * Verify: `git remote -v`
  * Syncing the fork withe upstream repo:
    * Fetch the branches and their respective commits from the upstream repository. Commits to BRANCHNAME will be stored in the local branch upstream/BRANCHNAME: `git fetch upstream`
    * Check out your fork's local default branch - in this case, we use `develop` FIXME need more clarity on this one:`git checkout develop(?)`
    * Merge the changes from the upstream default branch - in this case, `upstream/develop` - into your local default branch. This brings your fork's default branch into sync with the upstream repository, without losing your local changes:`git merge upstream/develop`
    * Push the changes to the fork:`git push`


### LOGOS

  * [What Does the Color of Your Logo Say About Your Business? (Infographic)](http://www.entrepreneur.com/article/232401)


### Creating favicon

  * Generate a **16x16** image (Gimp, Inkscape, etc) eg. favicon.png
  * Convert it to a ppm or pnm format eg: `$ pngtopnm favicon.png > favicon.pnm `
    * **NOTE** If you have more than 256 colors, you'll get an error. You can quantize it to 256 using `$ pnmquant 256 favicon.pnm > temp.pnm; mv temp.pnm favicon.pnm`
  * Convert using the the utility `ppmtowinicon` : `$ ppmtowinicon -output favicon.ico favicon.pnm`

## Open-Source

**Resources**

- [Redhat: What is open source software?](https://www.redhat.com/en/topics/open-source/what-is-open-source-software)
- [opensource.com:Which open source software license should I use?](https://opensource.com/law/13/1/which-open-source-software-license-should-i-use?extIdCarryOver=true&sc_cid=701f2000001OH6fAAG)

### Licensing

- No copy left: MIT, new BSD
- Some copy left with patenting & licensing 
- All copy left: GPLs 
