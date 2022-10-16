---
sort: 1
---

# IT 

## WebSite

### Jekyll: Static Page on GitHub

[#jekyll](https://jekyllrb.com) | #github | [#jekyll-rtd-theme](https://github.com/rundocs/jekyll-rtd-theme) #website #static [[https://adamtheautomator.com/github-pages-jekyll/|#jekyll-tutorial]] [[https://jekyll-themes.com/jekyll-rtd/|#jekyll-rtd-userguide]]

This section shows you how to create a static web page using [[https://jekyllrb.com|Jekyll]] (and a Jekyll theme) and host it on github.

**PREPARING LINUX FOR JEKYLL**
  * First we need the prereqs on a Linux workstation. Following is for the ''Ubuntu'' on ''AWS'':
  * <code bash>sudo apt-get install ruby-full build-essential zlib1g-dev</code>
  * In order to load ''gem'' locally, add the following in ''.bashrc''
    * <code bash>export GEM_HOME=$HOME/gems</code>
    * <code bash>export PATH=$HOME/gems/bin:$PATH</code>
  * <code bash>gem install jekyll bundler</code>

**CONFIGURING JEKYLL**
  * Site-wise configuration are done using ''_config.yml''
  * See https://jekyll-rtd-theme.rundocs.io/ for config options.
  * :!:**IMPORTANT**:!: Option ''baseurl'' when testing a site that doesn't sit at the root of the server domain. See this [[https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/#:~:text=Set%20baseurl%20in%20your%20_config,baseurl%20to%20in%20your%20_config. | blog]] for more detail on it.
  * Someone changed this to ''/silicon-vlsi.github.io'' and all urls had duplicate domain eg ''https://silicon-vlsi.github.io/silicon-vlsi.github.io/content/projects.html'' and thus breaking the links.
  * Removed the ''baseurl'' and ''url'' as well since hosting on github automatiacally takes care of it. I think. It works so far.

**USING A JEKYLL TEMPLATE IN GITHUB**
  * Login to your github account eg. ''silicon-vlsi''
  * Navigate to the template repo (eg. [[https://github.com/rundocs/jekyll-rtd-theme | jekyll-rtd-theme]]) and click ''Fork''
  * Rename (from the repo's settings) the copied repo to the following format:
    * ''<username>.github.io''
    * eg. ''silicon-vlsi.github.io''
  * Give it few minutes to publish it and browse to ''http://silicon-vlsi.github.io'' to see the website!

**USING JEKYLL TO MAINTAIN THE SITE**
  * Clone the repo to your prepared Linux workstation:
    * <code bash>git clone https://github.com/silcion-vlsi/silicon-vlsi.github.io</code>
  * Change directory ''cd'' to ''silicon-vlsi.github.io'' and edit ''_config.yml'' change the info.
  * For the first time after clone, to get the dependencies:<code bash>bundle install</code>
    * <code bash>bundle update</code> FIXME Document this
  * Build the site again after the changes:<code bash>bundle exec jekyll build</code>
  * <code bash>git commit --all [--allow-empty] -m "comment"</code> FIXME: Document when we nee ''--allow-empty''
  * <code bash>git push</code>

**CONTENT MANAGEMENT**

The directory structure (USR tag indicates changes made by the user and SYS typically should be left untouched and synced with the original repo):
<code bash>
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

</code>

** SYNCING THE LOCAL FORK WITH ORIGINAL UPSTREAM REPO**
FIXME Refer a proper documentation for this and put some more detail in this documentation. 

  * Related github docs: [[https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/configuring-a-remote-for-a-fork | Config a remote for fork]], [[https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/syncing-a-fork | Syncing a fork]]
  * **Onetime** config remote upstream repo with the fork:
    * List the current configured remote repository for your fork.<code bash>git remote -v</code>
    * Specify the remote upstream repository that will be synced with the fork:<code bash>git remote add upstream  https://github.com/rundocs/jekyll-rtd-theme.git</code>
    * Verify: ''git remote -v''
  * Syncing the fork withe upstream repo:
    * Fetch the branches and their respective commits from the upstream repository. Commits to BRANCHNAME will be stored in the local branch upstream/BRANCHNAME: <code bash>git fetch upstream</code>
    * Check out your fork's local default branch - in this case, we use ''develop'' FIXME need more clarity on this one:<code bash>git checkout develop(?)</code>
    * Merge the changes from the upstream default branch - in this case, ''upstream/develop'' - into your local default branch. This brings your fork's default branch into sync with the upstream repository, without losing your local changes:<code bash>git merge upstream/develop</code>
    * Push the changes to the fork:<code bash>git push</code>
====== LOGOS ======
  * [[http://www.entrepreneur.com/article/232401 | What Does the Color of Your Logo Say About Your Business? (Infographic) ]] : Entrepreneur
====== Creating favicon ======
  * Generate a **16x16** image (Gimp, Inkscape, etc) eg. favicon.png
  * Convert it to a ppm or pnm format eg: <code bash>$ pngtopnm favicon.png > favicon.pnm </code>
    * **NOTE** If you have more than 256 colors, you'll get an error. You can quantize it to 256 using <code bash>$ pnmquant 256 favicon.pnm > temp.pnm; mv temp.pnm favicon.pnm</code>
  * Convert using the the utility ''ppmtowinicon'' : <code bash>$ ppmtowinicon -output favicon.ico favicon.pnm</code>
