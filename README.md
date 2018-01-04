---
author:
  name: Anzhari Purnomo
  email: anzhari@merahputih.id
description: 'Host a Static Website with Hugo and NGINX on Ubuntu 16.04.'
keywords: [“hugo”, “nginx”, “static site generator“, “website”, “html”, “go”]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2017-12-28
modified: 2017-12-28
modified_by:
  name: Anzhari Purnomo
title: 'Host a Static Website with Hugo and NGINX on Ubuntu 16.04'
contributor:
  name: Anzhari Purnomo
  link: https://id.linkedin.com/in/anzhari
external_resources:
  - ‘[Introduction to Static Site Generator](https://davidwalsh.name/introduction-static-site-generators)’
  - ‘[Hugo Quickstart Guide](https://gohugo.io/getting-started/quick-start/)’
  - ‘[Hugo Install Guide](https://gohugo.io/getting-started/installing/)’
  - ‘[NGINX Install Guide](http://nginx.org/en/linux_packages.html)’
  - ‘[NGINX Beginners Guide](http://nginx.org/en/docs/beginners_guide.html)’


---

## Introduction

Hugo is a static site generator. Unlike systems that dynamically build a page with each visitor request (for example, Ruby, Python, or PHP based website), Hugo builds pages when you create or update your content. Since websites are viewed far more often than they are edited, Hugo is designed to provide an optimal viewing experience for your website’s end users and an ideal writing experience for website authors.

Websites built with Hugo are extremely fast and secure. Hugo sites can be hosted anywhere, including on Linode. Hugo sites run without the need for a database or dependencies on expensive runtimes like Ruby, Python, or PHP.
We think of Hugo as the ideal website creation tool with nearly instant build times, able to rebuild whenever a change is made.

This guide will covers on how to install Hugo, and then deploy the generated website on nginx web server.

{{< note >}}
This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the `sudo` command, see the [Users and Groups](/docs/tools-reference/linux-users-and-groups) guide.
{{< /note >}}

## Before You Begin

1.  Complete the Getting Started guide.

2.  Follow the Securing Your Server guide to create a standard user account, harden SSH access and remove unnecessary network services; this guide will use sudo wherever possible.  Do not follow the Configuring a Firewall section–this guide has instructions specifically for an nginx production server.

3.  Log in to your Linode via SSH and check for updates using apt-get package manager.      sudo apt-get update && sudo apt-get upgrade

## Open Corresponding Firewall Ports
In this case we’re using default web port 80 and hugo default port 1313 for draft, but this could be any port you specify later in the configuration file.

    sudo ufw allow ssh
    sudo ufw allow 80/tcp
    sudo ufw allow 1313/tcp
    sudo ufw enable

## Install Hugo

    sudo apt-get install hugo
    hugo version


## Create a New Site

    hugo new site quickstart

The above will create a new Hugo site in a folder named quickstart.

## Add a Theme
See themes.gohugo.io for a list of themes to consider. This quickstart uses the beautiful Ananke theme.

    cd quickstart;\
    git init;\
    git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke;\

Edit your config.toml configuration file and add the Ananke theme.

    echo 'theme = "ananke"' >> config.toml

From your `~/sites` directory, executes this command:

    cd quickstart                                                                                     

From your `~/sites/quickstart` directory, executes this command:

    git init                                                                                          

Initialized empty Git repository in `~/sites/quickstart/.git/`:

~/sites/quickstart

    git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke


## Add New Content
    hugo new posts/my-first-post.md

Edit the newly created content file if you want. Now, start the Hugo server with drafts enabled:

    hugo server -D

## Customize the Site
Your new site already looks great, but you will want to tweak it a little before you release it to the public.
Site Configuration
Open up `config.toml` in a text editor:

{{< file ~/sites/quickstart >}}
    baseURL = "https://example.org/"
    languageCode = "en-us"
    title = "My New Hugo Site"
    theme = "ananke"
{{< /file >}}

Replace the title above with something more personal. Also, if you already have a domain ready, set the baseURL. Note that this value is not needed when running the local development server.

{{< note >}}
Tip: Make the changes to the site configuration or any other file in your site while the Hugo server is running, and you will see the changes in the browser right away.
{{< /note >}}

## Generate Static Site for nginx

This generates your website to the public/ directory by default, although you can customize the output directory in your site configuration by changing the publishDir field.
The site Hugo renders into public/ is ready to be deployed to your web server:

    hugo

## Install nginx
To ensure compatibility of installation and with future updates, install nginx from the Ubuntu package repository using apt:

    sudo apt-get install nginx

## Configure nginx Virtual Hosting
Older versions of nginx specified site directories and other information in the main nginx.conf file, but newer versions, such as the ones included with Ubuntu 16.04, are more compartmentalized. As you read through this section, make note of each file’s contents and location so that you are familiar with the structure and know where to go if you need to customize one particular aspect of your web server.
