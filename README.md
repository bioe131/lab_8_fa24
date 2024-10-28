# Lab 8
You should run through this tutorial on the machine you intend to use for the final project. The assumption is that you will use your laptop or other personal machine, which simplifies creating and accessing a localhost webserver.

This lab will essentially walk you through the JBrowse2 Genome Browser tutorial found in the paper linked below. You should read through as there are many helpful comments, but note that in the tutorial version in this lab we provide some additional context and platform-specific assistance for non-linux machines.

https://currentprotocols.onlinelibrary.wiley.com/doi/10.1002/cpz1.1120

At the end, you will have a little interactive web app that lets you browse annotations in the human genome.

## 1. Platform differences
It should be possible to run the tutorial on either a Mac OS computer (Intel or M1), a Windows computer (Windows 10 or later), or a machine that runs a linux distribution. However, there are a few platform specific setup details: specifically, some of the necessary bioinformatics tools (like samtools) are only available pre-compiled for Unix operating systems, making Windows Subsystem for Linux an important tool - if you want to use a Windows machine for bioinformatics, this is going to be a crucial tool for many things. Please follow the platform-specific instructions below before proceeding to the "Install necessary tools" section.

### 1.1. Mac OS setup
Open a terminal and run the line below to install homebrew, a macOS package manager. This will make it easy for you to install necessary packages like apache2 and samtools. _You can skip this step if you already have brew installed._ 

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
If this doesn't work, visit https://docs.brew.sh/Installation for further installation options, including a .pkg installer that should be convenient and easy to use.

### 1.2. Windows setup
Enable and set up Windows Subsystem for Linux, using the default Ubuntu distribution. 

For newer versions of Windows, this command should handle it for you. Further details can be found at https://learn.microsoft.com/en-us/windows/wsl/install. 

_You can skip these steps if you already have WSL set up with a Debian or Ubuntu distribution._

```
wsl --install
```

You can check that `wsl` was installed properly by running `wsl -l -v`. If you are running an older Windows 10 version, you may instead need to follow the instructions here: https://learn.microsoft.com/en-us/windows/wsl/install-manual. (In this case, you have to select your Ubuntu version. We recommend Ubuntu 22.04.)

Next, set up your Linux username and password. You can launch WSL from the start menu and follow the prompts (see https://learn.microsoft.com/en-us/windows/wsl/setup/environment#set-up-your-linux-username-and-password). Make sure to record the password you choose, although when you launch WSL in future it should automatically sign you in.

**For all subsequent steps, run from within the WSL virtual machine. This way, you should be able to seemlessly run Unix applications and use the Linux instructions in subsequent steps.**

### 1.3. Linux setup
Make sure you are using a Debian or Ubuntu distribution. If you are, there should be no setup required before moving on. 

## 2. Install necessary tools
### 2.1. Node.js
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

### 2.2. @jbrowse/cli
Run the following commands in your shell. This uses the Node.js package manager to download the latest stable version of the jbrowse command line tool, then prints out its version.

```
sudo npm install -g @jbrowse/cli
jbrowse --version
```

### 2.3. System dependencies
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

## 3. Apache server setup
### 3.1. Start the apache2 server
Starting up the web server will provide a localhost page to show that apache2 is installed and working correctly. When discussing computer networking, localhost is a hostname that refers to the current computer used to access the network. Note that in WSL2, the linux subsystem may have a different IP address from your Windows OS, and so you will want to use that IP address to be able to find it and load the web page.

Linux
```
sudo service apache2 start
```

macOS
```
sudo brew services start httpd
```

Windows WSL2
```
# run the linux server launch command to launch the service
cat /etc/resolv.conf
```
The cat command will print out network configuration information, including the `nameserver` value, such as `nameserver 34.118.224.10`. 

### 3.2. Access the web server
Open a browser and type `http://localhost:8080/` into the address bar. You should then get to a page that says "**It works!**". If you are using WSL2 on Windows, then instead go to `http://XX.XXX.XXX.XX:8080/`, where Xs are replaced with the appropriate nameserver IP address. 

### 3.4. Verify apache2 server folder
Apache2 web servers serve files from within a root directory. This is configurable in the httpd.conf configuration file, but you shouldn't have to change it. For a normal linux installation, the folder should be `/var/www` or `/var/www/html`, whereas when you install on macOS using brew it will likely be in `/opt/homebrew/var/www` or `/opt/homebrew/var/www/html`. Verify that one of these folders exists (it should currently be empty, but we will now populate it with JBrowse 2). If you have e.g. a www folder with no www/html folder, and your web server is showing the "It works!" message, you can assume that the www one is the root directory. 

Take note of what the folder is, and use the command below to store it as a command-line variable.
```
export APACHE_ROOT='/path/to/rootdir'
```
### 3.5. Download JBrowse 2
First create a temporary working directory as a staging area. You can use any folder you want, but moving forward we are assuming ~/tmp in your home folder.

```
mkdir ∼/tmp
cd ∼/tmp
```

Next, download and copy over JBrowse 2 into the root dir, setting the owner to the current user and printing out the version number. This version doesn't have to match the command-line jbrowse version.

```
jbrowse create output_folder
sudo mv output_folder $APACHE_ROOT/jbrowse2
sudo chown -R $(whoami) $APACHE_ROOT/jbrowse2
```

### 3.6. Test your jbrowse install
In your browser, now type in `http://yourhost/jbrowse2/`, where yourhost is either localhost or the IP address from earlier. Now you should see the words "**It worked!**" with a green box underneath saying "JBrowse 2 is installed." wit some additional details. 

## 4. Load and process test data
### 4.1. Download and process reference genome
Make sure you are in the temporary folder you created, then download the human genome in fasta format.
```
export FASTA_ROOT=https://ftp.ensembl.org/pub/release-110/fasta/homo_sapiens
wget $FASTA_ROOT/dna/Homo_sapiens.GRCh38.dna_sm.primary_assembly.fa.gz
```
Unzip the gzipped reference genome, rename it, and index it. This will allow jbrowse to rapidly access any part of the reference.
```
gunzip Homo_sapiens.GRCh38.dna_sm.primary_assembly.fa.gz
mv Homo_sapiens.GRCh38.dna_sm.primary_assembly.fa hg38.fa
samtools faidx hg38.fa
```
### 4.3. Load genome into jbrowse
```
jbrowse add-assembly hg38.fa --out $APACHE_ROOT/jbrowse2 --load copy
```
## 4.4. Download and process genome annotations
Still in the temporary folder, download to the GFF3 format. 
```
export GFF_ROOT=https://ftp.ensembl.org/pub/release-110/gff3/homo_sapiens
wget $GFF_ROOT/Homo_sapiens.GRCh38.110.chr.gff3.gz
gunzip Homo_sapiens.GRCh38.110.chr.gff3.gz
```
Use jbrowse to sort the annotations. jbrowse sort-gff sorts the GFF3 by refName (first column) and start position (fourth column), while making sure to preserve the header lines at the top of the file (which start with “#”). We then compress the GFF with bgzip, and index with tabix. The tabix command outputs a file named genes.gff.gz.tbi in the same directory, and we then refer to “genes.gff.gz” as a “tabix indexed GFF3 file”.
```
jbrowse sort-gff Homo_sapiens.GRCh38.110.chr.gff3 > genes.gff
bgzip genes.gff
tabix genes.gff.gz
```
### 4.5. Load annotation track into jbrowse
```
jbrowse add-track genes.gff.gz --out $APACHE_ROOT/jbrowse2 --load copy
```
### 4.6. Index for search-by-gene
Run the “jbrowse text-index” command to allow users to search by gene name within JBrowse 2.

In the temporary work directory, run the following command.
```
jbrowse text-index --out $APACHE_ROOT/jbrowse2
```

## 5.0 Use your genome browser to explore a gene of interest
### 5.1. Launch JBrowse2
Open `http://yourhost/jbrowse2/` again in your web browser. There should now be several options in the main menu. Follow the guide in the "Launch the JBrowse 2 application and search for a gene in the linear genome view" section of https://currentprotocols.onlinelibrary.wiley.com/doi/10.1002/cpz1.1120 to navigate to the gene search and try browsing a few genes.

### 5.1. Explore a specific gene of interest
Using the gene search function you've set up, look up a gene with disease-relevant function. You can use any academic articles you like to identify a gene of interest - one example might be to look up a cancer repressor gene, or a gene from your metabolic pathway lab earlier in the semester that might have mutations associated with metabolic diseases. 

In assignment.md, answer the prompts.

# Submission
Commit your assignment.md file with the prompts answered, then push to your remote repository and provide the link to your repo in the bCourses submission form.
