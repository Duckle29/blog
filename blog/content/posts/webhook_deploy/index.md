---
title: "Webhook deploy"
date: 2022-12-29T01:11:00+01:00
draft: false
author: "Mikkel Jeppesen"
---

When I set up this blog I wanted it to be as low friction as possible to write and publish posts.
I also wanted it to be a very simple blog site. Just serve the content, no faff.

That lead me to [HUGO](https://gohugo.io/) and the [m10c](https://github.com/vaga/hugo-theme-m10c) theme. 
I've been very happy with that choice. Posts are easily and quickly written in markdown (with some custom shortcodes for
download links and image resizing).

When it then came to deploying my new posts, I didn't want to have a tedious process of pushing to github, connecting
to my server over ssh and then pulling the changes. I wanted to automate this.

For that purpose, I wrote a very simple and small [webhook_server](https://github.com/Duckle29/webhook_server) using 
Flask and Flask-Hookserver. The server just sits and listens for webhooks from github, and then looks if it has a site
configured for the repo that the hook comes from. If it does, it updates the site and optionally runs some deploy commands

Lets look at an example config (the example config from the repo in fact)
```ini
[main]
github_secret = superSecretKey
endpoint = /deploy

# This should be the full repo name of the repository you want to deploy
[StartBootstrap/startbootstrap-resume]
clone_uri = https://github.com/StartBootstrap/startbootstrap-resume
path = /srv/example.com  # The path you want to deploy it to
production_branch = prod
# A deploy / build command can be specified here.
command = "cd site/stuff/ && hugo && echo funstuff"

[StartBootstrap/startbootstrap-clean-blog-jekyll]
clone_uri = https://github.com/StartBootstrap/startbootstrap-clean-blog-jekyll
path = /srv/blog.example.com
```

Under the `[main]` section, there's a github secret, and and endpoint.  
The server expects to run behind a proxy, and then receive webhooks at some endpoint. In this case the endpoint is /deploy
It also expects a secret key to be part of the webhook, to ensure random people can't mess with it. 

From there, each section in the config is a repo you want to listen to webhooks for. For this blog, that'd be 
`[Duckle29/blog]`

Then each site/repo need a few settings:
- *clone_uri* is the uri to clone the repo from.
- *path* is where to clone the files to

optionally:
- *production_branch* if you don't want to monitor the main branch, you can specify a production branch
- *command* If you want to run a command after pulling the changes, you can put this here.
For this blog that's `cd blog && hugo`

and voila. Very easy way to have simple static websites deployed. 