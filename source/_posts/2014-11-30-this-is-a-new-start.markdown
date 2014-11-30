---
layout: post
title: "This is a new start! Welcome OctoPress!"
date: 2014-11-30 19:25:07 +0200
comments: true
categories: blog, howto,
---
Every so often I get asked to write an article on a certain specific subject. I am a bit ashamed to admit that I very rarely respond to those requests and outside of work where I am somewhat required to submit technical documentation, I don't get around to writing about my interesting exploits in the world of tech. 

Hopefully, this blog will be the beginning of an era where I actually get around to vocalising my daily exploits on a more regular basis. So to kick off my new blog, I'll write a short article about OctoPress, how to set it up and why I decided to go with a statically generated blog as apposed to something like WordPress...

<!--more-->
###Why OctoPress?
Simplicity. In an industry that is so overwhelmed by dynamic this and elastic that, I have come to really appreciate the simple things in life. I love the idea that my blog can be hosted directly from a AWS S3 bucket or GitHub pages without a single complex system behind it. OctoPress is elegant, easy and beautiful. The possibilities is endless and deploying it and updating it on the go is one of the easiest things you can think of doing. 
####Octostrap... Really? 
Yes! I don't care what anyone says, the Twitter Bootstrap framework is possibly the best thing that has ever been done for developers that frequently develop responsive web applications. I also love the design elements of Bootstrap, even the basic "off the shelf" design that it ships with. However, that doesn't stop you from really easilly adding your own flare to the framework. Themes like BootSwatch makes it so easy to extend on the standard look and web apps like [Bootstrap Magic ](http://pikock.github.io/bootstrap-magic/index.html) make it easy as pie to create your own personal look and feel.
###Fine, Tell me how to set it up...
Sure, but first off, some backround on what you will need. Everyone knows I am a huge Linux fan, so obviously I will be guiding you on how to do this on a Linux Desktop, more specifically an Ubuntu Based, Mint Linux Desktop, but this does not mean that you should be using the same. OctoPress will run on anything that runs Ruby, and well, thats everything except your fancy Chinese Smart Toilet.

#####Requirements
- Ruby ~> 1.9.3
- Ruby Gems ~> 1.8.23
- Git ~> 1.9.1

######Step 1: Install Git
If you haven't already got it, install Git on your Desktop. Using a Linux Desktop like Ubuntu you can use apt-get, or for Fedora, yum.
``` bash Install Git using the terminal
sudo apt-get install git
```
######Step 2: Clone into the OctoPress Repo.
``` bash Make Dir and Clone Repo
git clone git://github.com/imathis/octopress.git myblog
cd myblog
```

######Step 3: Configure and Install the Required Gems.
``` bash Install and Configure Gems
sudo gem install bundler -V
sudo bundle install
```
Okay, Okay. So here I need to stop and give a bit of an explanation. The first command installs bundler. [Bundler](http://bundler.io/) is a Ruby Library that allows you to add a file to the source of your application with a list of all it's dependencies. When you install it, you only need to execute Bundler and it will install all of the necessary Gems. It's pretty important that you run both these commands as `sudo` otherwise the Gems will most likely fail to install. 

######Step 4: Install OctoPress
``` bash Install OctoPress
rake install
```
At this point we have all the dependencies required by Octopress and we can now use Rake to install and configure it for us. [Rake](https://github.com/ruby/rake) is a Make-like program implemented in Ruby. Tasks and dependencies are specified in standard Ruby syntax. Just like Make, it's pretty powerfull and pretty awesome, so I would suggest you check it out if you are into creating CLI tools and whatchamathings. 

######Step 5: Install OctoStrap Theme.
``` bash Install OctoStrap
git clone https://github.com/kAworu/octostrap3.git .themes/octostrap3
rake "install[octostrap3]"
rake generate
```
This step essentially downloads the source files from the Git repo and saves them in the .themes folder, in the root directory of OctoPress. You then activate the theme by running the rake "install[themename]" command which moves the correct files into the correct places. There are [many OctoPress Themes](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes) available, but like I mentioned earlier, I am a big Bootstrap fan. Also, if you want to modify the OctoStrap Theme to personally fit your needs, follow their [Docs](http://kaworu.github.io/octostrap3/setup/pick-a-theme/), The generate command generates all of the HTML for the new theme. You will run rake generate every time before you publish your blog.

######Step 6: Configure OctoPress
``` ruby Configure OctoPress https://github.com/imathis/octopress/blob/master/_config.yml Link to Gitbhub Source
url: http://blog.mandusmomberg.com
title: Mandus Momberg's Blog
subtitle: Everything Big and Interesting.
author: Mandus Momberg
simple_search: https://www.google.com/search
description: This blog is all about BigData, Linux, Windows and the Internet of Things. If it is cool, it belongs here. 
```
The OctoPress config can be found in the _config.yml file in the root folder of OctoPress. There is quite a few setting you can set here, but I am not going to go into too much detail on it. If you want to, you can read the [Official Documentation](http://octopress.org/docs/configuring/)

######Step 7: Start Bloggin
At this stage your blog has been set up and you can now start creating posts and pages. Depending on your theme, you might need to make changes to the Navigation Menu in the _config.yml file if you want to. 
To create a new Post, you simply run the command:
``` bash
rake new_post["title"]
``` 
To create a new Page, you can run: 
``` 
rake new_page["page name"]
```
Both these commands will create files in the `./source` directory. Respectivly in their own folders `_posts` and '_pages' 
If you want to learn more about posts and pages, and need some more guidance, you can read the [Official Docs](http://octopress.org/docs/blogging/)

###And Folks, Thats All She Wrote, For Now...
There is quite a bit I could be writing around deploying your blog but I felt that this should be another post on its own. Since you can deploy your Blog to Github Pages, S3 and a bunch of other places, I would rather want to spend some time speaking about the benifits and failures of all three and this post is already way to long for that. 

Thanks for reading this far and please feel free to leave a comment below.