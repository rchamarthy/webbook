---
title: "Blog with Hugo and Github" # Title of the blog post.
date: 2020-12-30
description: "Building a static website with Hugo, Github, and Vercel"
featured: true
draft: true
# menu: main
featureImage: "/images/2020-12/building-webbook/feature.png"
thumbnail: "/images/2020-12/building-webbook/thumbnail.png"
shareImage: "/images/2020-12/building-webbook/share.png"
categories:
  - Technology
tags:
  - blog
---

As it is customary, my first post is about building this website. This post
assumes that you have a basic understanding of git, GitHub, and web domains. The
focus will be on the tools chosen and integration of these tools.

## Goals

* Write content in your favorite text editor
* Manage content in source control (git)
* Automate deployment of the content to the website
* Keep your readers privacy intact

Quick tools summary:

* [git](https://git-scm.org)
* [Github](https://github.com)
* [VS Code](https://code.visualstudio.com)
* [Icons](https://realfavicongenerator.net)
* [Hugo](https://gohugo.io)
* [Hosting](https://vercel.com)

## [Hugo](https://gohugo.io)

Hugo is a static site generation tool written in [Go](https://golang.org). Hugo
has strong theme support and a variety of [themes](https://themes.gohugo.io).

Features that matter to me:

* Incremental build - Site generation is incremental and blazing fast
* Privacy compliance - [GDPR](https://gohugo.io/about/hugo-and-gdpr)
* Explicit security model - [Security](https://gohugo.io/about/security-model)

A full feature list is available [here](https://gohugo.io/about/features).

There are many other static site generators out there, and I picked Hugo because its
written in Go, and if required, I can customize or fix issues I may find in the
tool.

### [Install Hugo](https://gohugo.io/getting-started/installing)

```bash
brew update && brew install Hugo
```

### Pick a theme

To jump-start, your website, select a theme that suits your needs. After reviewing
a ton of Hugo themes and experimenting with a few, I finally settled on [hugo-clarity](https://themes.gohugo.io/hugo-clarity). A huge shout out to [Chip Zoller](https://neonmirrors.net)
and [Dan Weru](https://github.com/onweru) for this awesome theme. Design is clean
with no clutter what so ever, and has all the features required for a technical
blog. I particularly love the superb [rich code block](https://themes.gohugo.io/hugo-clarity/#features) functionality.

There are many ways to install a theme, but I prefer the cool go mod approach:

```bash
hugo new site yourSiteName
cd yourSiteName
git init
hugo mod init yourSiteName
```

NOTE: You will need the latest Hugo binary and Go installed on the system for
this method.

### Configure the theme

Most themes provide an ```exampleSite```, copy it over to your root directory, and edit the config.toml. You can find the config.toml for this site [here](https://github.com/rchamarthy/webbook/blob/main/config.toml).

```bash
cd yourSiteName
git clone https://github.com/chipzoller/hugo-clarity /tmp/hugo-clarity
cp -a /tmp/hugo-clarity/exampleSite/* .
rm -rf /tmo/hugo-clarity
```

### Customize logo and icon

Create a simple logo and favicon for the website. There are many free to use
icons available at [flaticon.com](https://www.flaticon.com). I used [draw.io](https://draw.io)
to create the logo and the icons and this [favicon generator](https://realfavicongenerator.net) to generate the website's icons. Unpack the icons into the
static folder.

### Local testing

Hugo comes with a built-in server that you can use to test your website live:

```bash
# Serve the files, including draft posts
hugo server -D
```

To generate the site:

```bash
# Generate content including drafts
hugo -D
```

The above command generates the full static content in the ```public``` folder, which can be
copied to your webserver.

## Github

Create a new empty repository for your website and push your content.

```bash
cd yourSiteName
# Make sure you configured your .gitignore correctly to ignore the addition of
# unnecessary files.
git add .
git commit -s -m "commit comment here"
git remote add origin "https://github.com/yourUserName/yourSiteName.git"
git checkout -b initial-commit
git push origin +initial-commit
```

### Actions and checks

Github support actions to define your deployment workflows. I strongly recommend
adding merge checks for your content for the following:

* Hyperlink Check

```yaml
# .github/workflows/check-md-links.yml
name: Check Markdown links

on: push

jobs:
  markdown-link-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - uses: gaurav-nelson/github-action-markdown-link-check@v1
        with:
          use-quiet-mode: "yes"
          use-verbose-mode: "yes"
```

* Spelling Check

```yaml
# .github/workflows/spell-check.yml
name: Spellcheck Action
on: push

jobs:
  build:
    name: Spellcheck
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: rojopolis/spellcheck-github-actions@0.5.0
      name: Spellcheck
```

To enable the checks, go to GitHub repository settings -> Branches. Add a branch
protect rule for your ```main``` branch and enable the following checks:

![Github Checks](/images/2020-12/building-webbook/github-checks.png)

## Hosting

When I started, I considered using ```github.io``` to host the website, looking
a bit further I found [vercel](https://vercel.com) and [render](https://render.com/).
Both of them offer static website hosting for free, with the upside of hosting APIs if you add services to your website. In the end, vercel's [cli support](https://vercel.com/docs/cli) sealed the deal.

1. Create an account using your Github account
2. Integrate with GitHub and allow access to your blog repo

Vercel supports ```staging``` and ```production``` deployments. Setup the ```main```
branch as the production branch. Render your content by pushing it to the ```main``` branch. To avoid accidental rendering, add
the vercel check to the main branch, as shown in the GitHub section.

### Custom Domains

If you have a custom domain like me, you need to set up a forwarding record with
your domain provider. Please note that you need to use the CNAME record for sub-domains
like [ravi.chamarthy.dev](https://ravi.chamarthy.dev). For more details on configuring
custom domains with vercel, refer to the [documentation](https://vercel.com/docs/custom-domains).

## Blog Workflow

Once you completed the above steps, you can focus on writing. The following is my workflow:

* Create a branch for your post

```bash
git checkout -b drafts/building-webbook
```

* Create a new post

```bash
hugo new post/2020-12/building-webbook.md
```

* Edit the content, I user vscode with vim, markdown all in one, code spell checker
* Test locally using ```hugo server -D```
* Commit and Push

```bash
git add .
git commit -s -m "blog: building webbook"
git push origin +drafts/building-webbook
```

* Create a pull request to the ```main``` branch
* Github actions will take over and give you a blessing to merge
* ```Rebase and Merge``` to the ```main``` branch
* Check your website for the new content

## Summary

With this workflow, you can focus on your writing and enjoy a familiar workflow to
manage and publish content to your website.
