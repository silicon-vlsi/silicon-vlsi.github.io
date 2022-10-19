---
sort: 1
---

# IT 

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

