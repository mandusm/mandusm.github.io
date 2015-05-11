---
layout: post
title: "Perfect Terminal"
date: 2015-05-09 12:28:57 +0200
comments: true
categories: 
---
I spend an exuberant amount of time working inside terminals and using ssh to manage remote servers. They range from the default terminal interface shipped with Linux, to the more outrageous OSX Based iTerm2. It's safe to say that because of this, I have spent much of my time customizing the way my terminals react and display information as well as store my SSH keys for easy authentication. In this BLOG post I will discuss the terminals I use and the different settings I invoke to make my time spent in them, more pleasurable. I'll also take a look at how to make SSH keys available across terminal sessions.  

<!--more-->
###An Example
To start off with, this is an example of my terminal in iTerm2 on OSX
{% img /images/blog_images/shell_preview.png %}

###Choices, Choices. 
As you probably know, there is a very large array of terminal emulators available out there. They are all different across Operating Systems, but most of them have the same basic functionality.   
For me the core requirements are...

 - Split Screen 
 - Auto Scroll
 - Infinite scroll-back
 - Broadcasting

With the above core requirements in mind, my terminals of choice, per OS, is...

 - Windows : [ConEmu](http://www.fosshub.com/ConEmu.html)
 - Linux : [Terminator](http://gnometerminator.blogspot.com/p/introduction.html)
 - OSX : [iTerm2](https://www.iterm2.com/)

###I feel pretty, O so Pretty
Most of the terminals nowadays come with the ability to theme and set the color schemes of your shell. A lot of my friends opt for the green-on-black combination. This hurts my eyes more than anything else after a few hours and although it looks cool and all Matrix-like, I've grown to not like it that much. Instead I prefer a soft white on a black background. That, together with some color coding to differentiate between different elements like folders and symlinks is what I have settled on. 

To implement this is pretty straight forward and only requires a little tinkering in your Bash Profile settings. Specifically, you need to set the PS1 and PS2 Environment Variables that dictates your Default and Continuous Interactive Prompt. I'm not going to go into this in too much detail, but if you want to read some more about it, you can read this awesome post at [TheGeekStuff](http://www.thegeekstuff.com/2008/09/bash-shell-take-control-of-ps1-ps2-ps3-ps4-and-prompt_command/) 

To have the changes persistent across multiple terminals you make the changes in `~/.bashrc`

``` bash ~/.bashrc
export CLICOLOR=1
export LSCOLORS=GxFxCxDxBxegedabagaced

function prompt {
  local BLACK="\[\033[0;30m\]"
  local BLACKBOLD="\[\033[1;30m\]"
  local RED="\[\033[0;31m\]"
  local REDBOLD="\[\033[1;31m\]"
  local GREEN="\[\033[0;32m\]"
  local GREENBOLD="\[\033[1;32m\]"
  local YELLOW="\[\033[0;33m\]"
  local YELLOWBOLD="\[\033[1;33m\]"
  local BLUE="\[\033[0;34m\]"
  local BLUEBOLD="\[\033[1;34m\]"
  local PURPLE="\[\033[0;35m\]"
  local PURPLEBOLD="\[\033[1;35m\]"
  local CYAN="\[\033[0;36m\]"
  local CYANBOLD="\[\033[1;36m\]"
  local WHITE="\[\033[0;37m\]"
  local WHITEBOLD="\[\033[1;37m\]"
  local RESETCOLOR="\[\e[00m\]"
 
  export PS1="$RED\u$PURPLE @ $GREEN\w:  $RESETCOLOR"
  export PS2="| → $RESETCOLOR"
}
 
prompt
```

###My SSH-Swiss Army knife
Now that my terminal reads easily and I can work without getting a headache every two hours, I needed to look at the way I was logging into remote servers. Usually this is done using SSH and my preferred method of authenticating is by using SSH Keys. If you want more details on how to set up key-less SSH you can read this article at [LinuxProblems](http://www.linuxproblem.org/art_9.html)

It's quite simple to start the ssh-agent and to add a key to it for easy authentication. 
``` bash Starting SSH-Agent and Adding Keys
eval `ssh-agent`
ssh-add /path/to/key
```

Although this is really quite easy, it means that I need to run this every time I open a new terminal. Fair enough, I can add it to my `~/.bashrc` file and it will automatically do this every time a new terminal is opened, but this will leave me with a huge amount of ssh-agent's running in the background and this isn't something I am happy with. 

To fix this, I started working on a solution which will check for a SSH-Agent and then re-use it. A few lines in I figured there should definitely be something like this available in the FOSS community. So after a bit of searching I found this project on Github. [ssh-find-agent](https://github.com/wwalker/ssh-find-agent)

It works great and it allows me to simplify my script to a huge extent. The first step is to clone this project to somewhere on my machine. I prefer it to be in my home folder in a hidden folder. `~/.ssh-find-agent`

Once this has been cloned, I can then simply add the following to my `~/.bashrc`
``` bash ~/.bashrc
#Set The SSH-Agent Environment
. ~/.ssh-find-agent/ssh-find-agent.sh
ssh-find-agent -a
if [ -z "$SSH_AUTH_SOCK" ]
then
   eval $(ssh-agent) > /dev/null
   ssh-add -l >/dev/null || alias ssh='ssh-add -l >/dev/null || ssh-add && unalias ssh; ssh'
   ssh-add /path/to/key &> /dev/null
fi
```

Now, every time I launch a new terminal it will look for a running SSH-Agent and add my keys to it, instead launching a new SSH-Agent process. If there is no running process, it will start one. 

### &> /dev/null
There you go, everything you need to know about having a fun and easy terminal session. How to easily log into a server using key-less SSH.
