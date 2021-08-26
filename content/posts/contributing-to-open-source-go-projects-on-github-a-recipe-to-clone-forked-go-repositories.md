---
title: "Contributing to Open Source Go projects on GitHub - A recipe to clone forked Go repositories"
date: 2019-06-08
draft: true
tags: 
  - golang
  - open source
  - git
  - github
categories:
  - programming
ShowToc: false
cover: 
  image: "/fork-go-repositories-on-github.png"
---
*This post was originally published on my Dev.to profile : [Contributing to Open Source Go projects on GitHub — A recipe to clone forked Go repos.](https://dev.to/penthaapatel/contributing-to-open-source-go-projects-on-github-a-recipe-to-clone-forked-go-repos-a87)*

Phew! It took me a while to figure out the entire process so I decided to write this post and I hope it'll further help everyone else get started. I managed to gather all the ingredients and curate the recipe to start working on an open source Golang project…so follow along.

Say you want to experiment with **org**'s new **tool** which is written in Go. So, you **go to get** (pun intended) the repo which you have **forked** on your GitHub account.


*Question* : Why your regular `go get` on the forked repo won't work?
*Simple Answer* : All your imports will be messed up. You'll have to change all the import statements that say something like `github.com/org/tool` to `github.com/yourGitHubID/tool` to make things work and your code will run into conflicts.

`go get` the original **org**'s repo. `go get` clones the repo into your `GOPATH` . `-u` flag updates the named packages and their dependencies.

    $ go get -u github.com/org/tool

Go to the directory where you just cloned the **org**'s **tool** repo

    $ cd $GOPATH/src/github.com/org/tool

Let's check what remote repos we have at this point. List out the remote repositories. `-v` for a verbose output.

    $ git remote -v
    origin https://github.com/org/tool (fetch)
    origin https://github.com/org/tool (push)

Add a add a new remote Git repository explicitly pointing at your fork. Here the shortname I used for the new remote is `fork`, you can name it whatever you like.

    $ git remote add fork https://github.com/yourGitHubID/tool.git

List out the remote repositories again to check and there you see it, a new remote has been added.

    $ git remote -v
    fork   https://github.com/yourGitHubID/tool.git (fetch)
    fork   https://github.com/yourGitHubID/tool.git (push)
    origin https://github.com/org/tool (fetch)
    origin https://github.com/org/tool (push)

Now that you have a remote that points to the forked repo on your GitHub account, you can start working. To start working on a new branch:

    $ git checkout -b experimentbranch

Make sure you have your branch updated with **org**'s branch. Then you can start making changes to the files on the experimentbranch `git add` your modified files and `git commit` your work  after testing the new features you've added.

Push changes to your fork.

    $ git push -u fork experimentbranch:experimentbranch

Then you can compare the changes you have made and submit a Pull Request to propose changes to be merged with the **org**'s repository.

*If you notice any corrections that need to be made in the post feel free to comment below! :)*