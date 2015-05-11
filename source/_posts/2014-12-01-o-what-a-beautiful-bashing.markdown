---
layout: post
title: "O! What a beautiful BASHing!"
date: 2014-12-01 18:52:58 +0200
comments: true
categories: bash scripting linux curl
---
On a daily basis I get faced with interesting problems for which I need to find simple and elegant solutions. The problem though is that these solutions need to work across a large, very diverse, set of environments. So I can't rely on languages like Ruby or Python to always solve my problems, because, the truth is, the environment in which the solution needs to work might not have it installed. But there is one thing, a common denominator across almost all Linux environments that I can comfortably rely on.    

BASH!

<!-- More -->
###Love is a strong word...
I find myself loving BASH and the Linux toolchain more and more every time I use it. I have never been let down by BASH and scripting simple solutions to otherwise complex problems is a breeze with tools like `cat`, `grep`, `awk`, `cut` and many more. 

Just this afternoon I was facing a problem where I needed to download a bunch of images from an [imgur](http://imgur.com/) album that a friend of mine in the USA sent me. For some odd reason, the "Download Album" Link just wasn't working. So, I decided to throw some bash at it.

In about 5 minutes I had a working script that would take the imgur album URL and download all jpg images in that album to a folder I specify. 
The main tool that I used for the backbone of this script was [cURL](http://curl.haxx.se/docs/manpage.html), and thus! THE imgur-curl was born!

###Olympic cURLing.
Okay, Okay. So the heading is a tad excessive, and the "curling" I am doing in this script is minimal at best, but I just had to throw a "Curling" reference in there somewhere.

####So what is cURL? 
[cURL](http://curl.haxx.se/docs/manpage.html) is a tool to transfer data from or to a server, using one of the supported protocols. It has a busload full of useful tricks like proxy support, user authentication, FTP upload, HTTP post, SSL connections, cookies, file transfer resume, Metalink, and more that has gotten me out of quite a few "slippery" situations. (See what I did there? Slippery... Curling.... Ice.. Get it? )

Aaanyway... So, cURL has gotten me out of some pretty tough spots. From having to automate the provisioning of a very complex networking infrastructure, to something as simple as scraping a website for jpg links.
The way you can use it is virtually limited only by your imagination and it's got a tiny footprint on your environment's resources. 

###We get it... Show us the script already!
Hold your horses already... Good things are worth... Ugh, who am I kidding, here you go.

{% include_code imgur-curl.sh  %}

As you can see, its a pretty simple script. Lines 2 - 13 is effectively there to make sure that the script will do what it is intended to do, download imgur jpg's with cURL.

Line 16 Is where the fun starts. On this line I set a variable CURL_GET to hold the output of my cURL request. I pass the options `-kL` to my request. I'll use this to do my scraping.  
`-k` Tells cURL to ignore any SSL Certificate issues it might detect, and just continue going.  
`-L` Tells cURL to adhere to any HTTP Redirects the server might issue and to follow them. If you don't do this, you might end up with a nice white page with text "Permanently Moved"  

On Line 17 I set a new variable which will contain a list of imgur jpeg names that I scraped from the page. 
I get these names by using `grep` and by passing `-o` and `-E` as arguments.   
`-o` Tells grep to only display the parts that match my query  
`-E` Allows me to define the query as a Regular Expression.   

I'm not going to go into much details on Regular Expressions in this post, but if you are interested, go over to [RegExr](http://regexr.com). Its a brillaint interactive website that makes learing and testing your Regular Expressions a breeze. 

Lines 18 to 26 is a [for](http://ss64.com/bash/for.html) loop that I use to iterate through the image names I stored in the IMG_SCRAPE variable and download them using curl. 
The imgur page that I scrape for the links also have "small" files that are used as sortof thumbnails to make the page load faster. I do some addition checking in my for loop for these files, and if I detect it, I skip it and I don't download the image since I am only interested in the high quality images. 

Now that I have the image name, I can download it from imgur using cURL.   
I once again break out the same `-kL` arguments to follow redirects and ignore SSL issues but this time I add `-o` and `--create-dirs`.    
`-o` is shorthand for specifying the output directory where I want the content I'm downloading to be stored. With `-o` I need to set the filename as well. You can use `-O` which will use the original filename.     
`--create-dirs` does exactly that, creates the directory I want to store the files if it does not exist.  

###Is that it? 
Yup, pretty much that. Super, Simple Stuff. I told you. But it's only the beginning. It's now up to you to go ahead and expand on this and make it your own..  

I decided to start a Github repository where I will be adding scripts that I find useful. There will be some oldies and some new ones as I keep developing them. If you want to take a look, you can find the repo here [Bash Script Repo](https://github.com/mandusm/BASH)