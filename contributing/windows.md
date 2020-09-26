---
title: Development using Windows
layout: default
---

## Setting up the development environment on Windows

This requires Windows 10, 2004 (2020 May Update) or later.

First we need to install the Windows Subsystem for Linux, Visual Studio Code and Docker. Then we will install and configure some software for the WSL2 distribution (Git). There may also be some suggestions on how to make things prettier.

### Visual Studio Code

[VS Code](https://code.visualstudio.com/) is recommended for the ease of integration with WSL2. It should "just work" once the rest of the parts are in place.

## WSL2 and Docker

Installing docker is easiest done by following [the instructions](https://docs.docker.com/docker-for-windows/install/) or if you are using [Windows Home](https://docs.docker.com/docker-for-windows/install-windows-home/).

After following those instructions you should be able to run the command `wsl --list --verbose` and see some installed machines, if they are running or not and what version they are. Hopefully there should be a docker one with version 2, otherwise something went wrong.

Finally, it is a good idea (unless you know otherwise) to issue the command `wsl --set-default-version 2` so future WSL software will use the faster version.

## WSL2 and Ubuntu

You could use any Linux distribution that is available for WSL2, but this guide is written with Ubuntu. These are found in the Microsoft store-app. When the installation is complete you should see the distribution you selected pop up when you run the `wsl --list --verbose` command in powershell. If it is listed as version 1 you can run (for Ubuntu) `wsl --set-version Ubuntu 2` to set the correct WSL version. This may require some additional (and prompted) installations.

### Ubuntu software

Update the installed distribution by opening the Ubuntu terminal, entering the command `sudo apt update && sudo apt upgrade` and waiting until things calm down. Then install git with the command `sudo apt install git`. Create a SSH key to use with Github (with no password, just hit enter when prompted by `ssh-keygen`) by executing `cd ~/.shh/ && ssh-keygen && cat id_rsa.pub` then copy and paste the public key to your Github account.

You should now be able to start developing by cloning the git repo, entering it and running the command `code .` to open that directory from the VS Code installed under Windows. The first time this is done a connecting software will be installed so that VS Code can speak to the WSL2 system.

Happy coding!

## Making things pretty

It can be really useful to set the terminal up in a way that give you some information about the state of things. [Scott Hanselman has a really nice guide](https://www.hanselman.com/blog/HowToMakeAPrettyPromptInWindowsTerminalWithPowerlineNerdFontsCascadiaCodeWSLAndOhmyposh.aspx) that will set up a more useful Windows Terminal, install some power line-enabled fonts and configure it to show some git status information.

To make this carry over to the WSL2 part I would recommend installing [the fish shell and oh-my-fish](https://github.com/oh-my-fish/oh-my-fish). But it's not a necessary thing.
