---
layout: post
author: dschaefer
date: 2016-12-01 14:32:00 +0500
title: Creating a github.io Blog Using Jekyll
---

For OnShift's 2016 Q4 Engineering Hack Day, I decided to create a blog for our department to share with the world our passion for the technologies we've used here at OnShift. I felt that it was very appropriate to write the first post, explaining what I did to setup this very blog.

## Technologies
I've decided to use Github.io for the hosting platform and jekyll&ndash;more specifically, `git@github.com:barryclark/jekyll-now.git`&ndash;for the blog site generator. My reasons for choosing these options are as follows:

- It's a free option
- There are no additional infrastructure that our team needs to manage
- It's using a technology we are all familiar with. i.e. Github
- I have experience with setting up my own blog using these technologies
- I've used other blogging platforms and they all seem overkill for my use case, as well as this use case

## Init

For the purposes of the initialization steps, I am assuming that the username/organization name is *OnShift*. Anywhere you see *OnShift*, replace it with your own username or organization.

1. Create an empty, public repository with the name *OnShift/OnShift.github.io*. Do **not** initialize the repo with any files. It must be an empty repo.
2. Run these commands locally, essentially copying the *git@github.com:barryclark/jekyll-now.git* repo into the empty repo you just created.
   <pre>
   <code>git clone git@github.com:OnShift/OnShift.github.io.git
   cd OnShift.github.io
   git checkout master
   git remote add barryclark git@github.com:barryclark/jekyll-now.git
   git pull barryclark master
   git push origin master</code>
   </pre>

The reason I chose to *copy* the repo instead of forking, is because we will be making a significant number of changes to the repo and it deviate so much that it would not make sense to contribute to the original project using the fork. If the original repo were to have any improvements that we would like to have in our our repo, we are still able to pull in the changes either by pulling from the original repo or by creating a git patch and apply it over top our repo. Either way, we will most likely need to resolve merge conflicts.

## Configure and Customize
...
