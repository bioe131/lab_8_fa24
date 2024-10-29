# AWS Instructions

Amazon Web Services provides a powerful platform for web hosting and running a wide variety of heavy-lift-compute tasks. With great power, however, comes responsibility: be careful when starting up instances, to make sure you aren't starting one that will charge you a bunch of money, and be sure to pause your instances when you aren't using them. Terminate the instance when you are done with it.

## 1. Create an account with a free trial
First, if you don't already have an account, create one at https://aws.amazon.com/free/. 

## 2. Navigate to launch instance
Sign into the console and find the link for EC2

![EC2](./screenshots/Screen%20Shot%202024-10-29%20at%203.46.15%20PM.png)

Click the orange Launch Instance button

![Launch instance](./screenshots/Screen%20Shot%202024-10-29%20at%203.47.45%20PM.png)

## 3. Configure the instance
You can mostly use the default settings. However, make sure to provide a descriptive name, select `ubuntu` from the AMIs list, pick the free-tier-eligible `t2.micro` instance type, and check to allow HTTP and HTTPS traffic (this will let you access your web server from your own computer's browser). Increase the default disk space to 30 GiB so you can accomodate the full human reference genome and annotations. Click **Launch instance** after double checking your configuration.

You may also want to select Proceed without key pair and just use the in-browser terminal connection to complete your work.

![select ubuntu](./screenshots/Screen%20Shot%202024-10-29%20at%202.10.40%20PM.png)
![select t2.micro](./screenshots/Screen%20Shot%202024-10-29%20at%202.10.59%20PM.png)
![enable HTTP and HTTPS](./screenshots/Screen%20Shot%202024-10-29%20at%202.11.10%20PM.png)
![increase disk size](./screenshots/Screen%20Shot%202024-10-29%20at%202.11.19%20PM.png)

## 4. Connect to the instance
You should land on a page that lists your active instances. 

![aws instance list](./screenshots/Screen%20Shot%202024-10-29%20at%202.13.25%20PM.png)

Click through to the one you just started. You should now see a status page like this:

![aws instance status](./screenshots/Screen%20Shot%202024-10-29%20at%202.13.16%20PM.png)

Click the connect button. That will take you through to a terminal session wherein you can follow the setup instructions in README.md for this repository.

## 5. IP address for your web server

The public IP address is highlighted below. You can access your apache web server, once you set it up, by simply going to `http://ipaddress` - no ports required.

![public ip address](./screenshots/Screen%20Shot%202024-10-29%20at%202.53.28%20PM.png)