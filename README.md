[![Travis-CI Build Status](https://travis-ci.org/isteves/two-bookdowns-example.svg?branch=master)](https://travis-ci.org/isteves/two-bookdowns-example)

## Overview

This is a skeleton repo for building two [bookdown](https://bookdown.org/home/about.html) books from one repository. The repo is based on two other bookdown demo repos:

- [bookdown-start](https://github.com/seankross/bookdown-start)
- [bookdown-demo](https://github.com/rstudio/bookdown-demo).

You can view the two bookdown books here: [dir1](https://isteves.github.io/two-bookdowns-example/dir1/) & [dir2](https://isteves.github.io/two-bookdowns-example/dir2/)

## Updating the book

If you have the repo connected to RStudio as a project (for example, via [this guide](https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN)), updating the book is easy!  Just edit the *.Rmd, stage & commit the changes, and push to the online repository! Travis will do the rest.

But first, you have to make sure Travis is hooked up to your repo, which is a bit trickier.

## Using Travis

Travis is used for "continuous integration" for R packages and in this case, books! It runs a series of checks to make sure the minimum requirements are met to make everything run smoothly and cohesively (read more in this [Beginner's Guide to Travis-CI for R](https://juliasilge.com/blog/beginners-guide-to-travis/). In addition, you can tell it to complete additional tasks by editing the `script` section of the `.travis.yml` file. For example, in this repo, we use the following:

```
script:
  - Rscript -e 'xfun::in_dir("dir1", bookdown::render_book("index.Rmd", "bookdown::gitbook"))'
  - Rscript -e 'xfun::in_dir("dir2", bookdown::render_book("index.Rmd", "bookdown::gitbook"))'
  - cp -vr dir1/dir1 docs
  - cp -vr dir2/dir2 docs
```

This renders all the *.Rmd files in each of the two directories (`dir1` and `dir2`) into a book, which is saved into the `output_dir` (specified in `_bookdown.yml`). In order to link the outputs of both files to [GitHub Pages](https://pages.github.com/), I've moved them into the `docs` folder using `cp -vr dirX/dirX docs`. These directories don't actually show up in the `docs` folder of the repo, but do show up in the `gh-pages` branch. (Not sure why, but would love to know!)

I'm not 100% on the order of the steps, but the following needs to happen:

### 1. Activate Travis for your repo.

Make an account if you haven't already. Otherwise, just head on over to https://travis-ci.org/profile/YOUR_USERNAME. You may have to sync your account if you made the new repo recently. Once it shows up in the repo list, just flip the switch to activate!

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/travis-switch.png)

### 2. Activate GitHub Pages in your repo. 

Click on **Settings** from the home page of your repo and scroll down to the **GitHub Pages** section. For the source, choose the `gh-pages branch` option. If you don't see it now, choose the `master branch /docs folder` to start and come back to change it after the next few steps. (That's what I had to do.)

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/repo-settings.png)
![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/github-pages.png)

### 3. Link your repo to Travis using an access token.

Navigate to your account settings (click your profile picture at the top-right corner of GitHub and then **Settings**). Under **Developer settings**, click **Personal access tokens** and then the `Generate new token` button. Add a token description and check the box next to `repo` under **Select scopes**. Scroll down and click `Generate token` when you finish. Copy the token to the clipboard (there's a handy button to make it super easy) and then go to Travis.

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/generate-token.png)
![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/access-token.png)

Navigate to your repo's Travis page (https://travis-ci.org/USERNAME/REPONAME) and select **More options** --> **Settings**. The **Environmental Variables** section is the one you need to change.

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/travis-settings.png)

For the `Name` field in the **Environmental Variables**, enter `GITHUB_TOKEN` (or however you've defined it in the `deploy` section of `.travis.yml`). For the `Value` field, paste in the access token you copied from GitHub. 

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/travis-env-var.png)

### 4. Update the YML files in your repo. 

Go through the files and make sure you've edited them to make them specific to your repo.

- `.travis.yml`: any changes to the directory names should be reflected in the `script` section
- `_bookdown.yml` (inside `dir1` and `dir2`): the output directory should be `dirX` (where X is 1 or 2) or whatever you rename the directories to. Change the book filename as you see fit (not critical to Travis working)
- `_output.yml`: changes should not affect Travis (just don't mess with the gitbook section too much)

### 5. Test it out! 

If everything is set up correctly, then Travis will be happy :green_heart:. If not, check through your YML files and Travis/GitHub repo settings. 

For example, I got this error while I was testing things out:

![](https://raw.githubusercontent.com/isteves/two-bookdowns-example/master/docs/travis-error.png)

I fixed it by going through step 3 (again).

**Overall, I found Travis/YML/bookdown a bit tricky to debug, but the solution is out there! You can check out more resources below:**

## Sources

- Bookdown: [Authoring Books with R Markdown](https://bookdown.org/yihui/bookdown/)
- Blogpost: [How to Start a Bookdown Book](http://seankross.com/2016/11/17/How-to-Start-a-Bookdown-Book.html)
- Blogpost: [A Beginner's Guide to Travis](https://juliasilge.com/blog/beginners-guide-to-travis/)
- Reference: [GitHub Pages deployment via Travis](https://docs.travis-ci.com/user/deployment/pages/)
- Reference: [Travis yml for R](https://docs.travis-ci.com/user/languages/r)
- Reference: [Requirements for the DESCRIPTION file](https://cran.r-project.org/doc/manuals/r-release/R-exts.html#The-DESCRIPTION-file)

