Title: Setup Pelican
Date: 2014-08-10 15:23
Category: writing
Tags: pelican
Slug: setup-pelican

Here is the simply step by step guide to setup pelican (3.4) on github pages

## Install pelican

```
virtualenv pelican-env
source pelican-env/bin/activate
pip install pelican, markdown
```

## Bootstrap the blog

```
mkdir myblog
cd myblog
pelican-quickstart
```

## Connect to Github Pages

```
cd myblog
git clone git@github.com:username/username.github.com.git output
```

* if you have existing files in this repo, delete all of them

* github app is your best friend if you are git beginner

## Edit the blog locally

* run local server

```
make devserver
```

* write a post

```
vim myblog/content/newpost.md
```

## Publish blog to github pages using git

Again, use Github App

## Change theme

download the desired theme (e.g.pelican-octopress-theme) into pelican-env/lib/python2.7/site-packages/pelican/themes/pelican-octopress-theme-master

In pelicanconf.py:
```
THEME = 'pelican-octopress-theme-master'
```

## Custom domain name

```
cd myblog/output/
touch CNAME
echo xxx.example.com > CNAME
```
