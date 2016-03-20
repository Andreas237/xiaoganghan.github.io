Title: Setup SCM Manager
Date: 2014-06-26 11:02
Category: Coding
Tags: version-control, git, mercurial
Slug: setup-scm-manager

[SCM Manager](http://www.scm-manager.org/) is an easy to use Git/Mercurial/Subvision repositories server. It's written in Java and very easy to install and config (web-interface). It is standalone (no apache or database required) and provides user/group/permission management.

I primarily use Mercurial. This post is the step-by-step guide to use it as a Mercurial server on Windows servers.

#### Step 0 (dependencies)
1. Install [JRE](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)
2. Install Python and Mercurial (don't forget to add both of them to System Path)

#### Step 1
1. Download [SCM Manager](http://www.scm-manager.org/download/)
2. [Run SCM Manager as a service/daemon](https://bitbucket.org/sdorra/scm-manager/wiki/daemons): `scm-server.bat install` and start the *scm-server* service manually in the Windows services panel (change *Startup Type* from _Manual_ to _Automatic_ if necessary).
3. Open localhost:8080 and login with default account: scmadmin/scmadmin
4. Open the _Repository Types_ panel and check the HG Binary path and Python Binary path are correctly located. (If not, click _Start Configuration Wizard_ button to config from local installation. The _download and install_ function has bug and may not install python properly.)
5. Add new repository under the _Repository_ panel. If everything works, you will be able to access to the repository via its url and clone the repo via: `hg clone http://scmadmin@ip:8080/scm/hg/testhg_repo` on your local machine.

update: It is much easier to get SCM Manager setup on Linux. To run it as a daemon, just add `JAVA_HOME=/usr/lib/jvm/(yourjdkdir)` to `bin/scm-server` and run `sudo ./scm-server start`. Step 1.4 above is not required.

