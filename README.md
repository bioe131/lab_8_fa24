# Lab 8
You should run through this tutorial on the machine you intend to use for the final project. The assumption is that you will use your laptop or other personal machine, which simplifies creating and accessing a localhost webserver.

This lab will essentially walk you through the JBrowse2 Genome Browser tutorial found in this paper: https://currentprotocols.onlinelibrary.wiley.com/doi/10.1002/cpz1.1120

At the end, you will have a little interactive web app that lets you browser annotations in the human genome.

We will provide some additional context and platform-specific assistance for non-linux machines.

## Platform differences
It should be possible to run the tutorial on either a Mac OS computer (Intel or M1), a Windows computer (Windows 10 or later), or a machine that runs a linux distribution. However, there are a few platform specific setup details: specifically, some of the necessary bioinformatics tools (like samtools) are only available pre-compiled for Unix operating systems, making Windows Subsystem for Linux an important tool - if you want to use a Windows machine for bioinformatics, this is going to be a crucial tool for many things. Please follow the platform-specific instructions below before proceeding to the "Install necessary tools" section.

### Mac OS setup
Open a terminal and run the line below to install homebrew, a macOS package manager. This will make it easy for you to install necessary packages like apache2 and samtools.

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
If this doesn't work, visit https://docs.brew.sh/Installation for further installation options, including a .pkg installer that should be convenient and easy to use.

### Windows setup
Enable and set up Windows Subsystem for Linux, using the default Ubuntu distribution. 

For newer versions of Windows, this command should handle it for you. Further details can be found at https://learn.microsoft.com/en-us/windows/wsl/install. 

```
wsl --install
```

You can check that `wsl` was installed properly by running `wsl -l -v`. If you are running an older Windows 10 version, you may instead need to follow the instructions here: https://learn.microsoft.com/en-us/windows/wsl/install-manual. (In this case, you have to select your Ubuntu version. We recommend Ubuntu 22.04.)

Next, set up your Linux username and password. You can launch WSL from the start menu and follow the prompts (see https://learn.microsoft.com/en-us/windows/wsl/setup/environment#set-up-your-linux-username-and-password). Make sure to record the password you choose, although when you launch WSL in future it should automatically sign you in.

**For all subsequent steps, run from within the WSL virtual machine. This way, you should be able to seemlessly run Unix applications and use the Linux instructions in subsequent steps.**

### Linux setup
Make sure you are using a Debian or Ubuntu distribution. If you are, there should be no setup required before moving on. 

## Install necessary tools
### Node.js
Node.js is a cross-platform JavaScript runtime environment that will make is easy to run JBrowse2 command-line tools.

The following install code should run on both macOS and Linux. See https://nodejs.org/en/download/package-manager for more detail.

```
# installs fnm (Fast Node Manager)
curl -fsSL https://fnm.vercel.app/install | bash
# activate fnm
source ~/.bashrc
# download and install Node.js
fnm use --install-if-missing 20
# verifies the right Node.js version is in the environment
node -v # should print `v20.18.0`
# verifies the right npm version is in the environment
npm -v # should print `10.8.2`
```

### @jbrowse/cli
Run the following commands in your shell. This uses the Node.js package manager to download the latest stable version of the jbrowse command line tool.

```
sudo npm install -g @jbrowse/cli
jbrowse --version
```

### System dependencies
Install wget (if not already installed), apache2, samtools, and tabix. 

wget is a tool for retrieving files over widely-used Internet protocols. 

apache2 allows you to run a web server on your machine.

samtools and tabix, as we have learned earlier in the course, are tools for processing and indexing genome and genome annotation files.

Linux

```
sudo apt install wget apache2 samtools tabix
```

macOS

```
brew install wget apache2 samtools tabix
```

## Apache server setup
