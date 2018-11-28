# Linux Server Configuration

### Project Description

This is the third project in the Udacity Full-Stack Nanodegree. The project takes a baseline installation of a Linux Distribution and prepare it to host web applications, including installing updates and securing it from a number of attack vectors.

- IP Address: 18.185.16.61

- Accessible SSH port: 2200

- Application URL: http://ec2-18-185-16-61.eu-central-1.compute.amazonaws.com

- SSH Connection: ssh -i ~/.ssh/[keyFile] grader@18.185.16.61 -p 2200

This summary will hopefully walk you through all the steps you need to secure your server, install dependinces and get your web application up and running in no time!

### Walkthrough Steps
 

## Step 1: Get an Amazon LightSail Server

[Follow the link to start up your own LightSail Server](https://aws.amazon.com/)

1. Sign up to the Amazon Service
2. Once logged in, under **Build a solution tab**, click **Build using virtual servers with LightSail**
3. Create your instance: 
    a. Leave the Instance Location as the default (unless you want to change it! Go ahead!)
    b. Select **Linux/Unix** from the **Pick your instance image**
    c. Click **OS Only** and select **Ubuntu 16.04 LTS**
    d. Select the lowest tier of the payment plans. If you complete the project within a month and shut it down, the price is zero anyway!
    e. Give your instance a hostname. Can be anything you want.
    f. Wait for it to start up. Once it says running, you're in business!

## Step 2: Configure LightSail Firewall

Before anything, make sure you configure your server firewall to allow the non-default port number.

    1. Click on your instance name
    2. Click on **Networking**
    3. Under **Firewall**, click **Add Another**
    4. Keep everything as default and simply enter the port number **2200**
    5. Click Save

Now you are ready to start configuring your instance!

## Step 3: Update, Secure!

    1. In the same line as **Networking**, click on **Connect**
    2. Click the shiny orange button **Connect using SSH**

You are now inside your instance!

    3. Run the following commands:
        ```
        sudo apt-get update
        sudo apt-get upgrade
        sudo apt-get autoremove
        
        ```
This will ensure your instance is updated to the latest version of its packages. 

    4. Run the following command:
        `sudo nano /etc/ssh/sshd_config`
        
Around line 5-6, change **PORT 22** to **PORT 2200** 
Save the file: `Ctrl + X Y Enter`

Now its time to configure the Uncomplicated Firewall (UFW)** to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

    5. Back in the terminal, run the following commands:
        ```
        sudo ufw status                     # check the status of the firewall
        sudo ufw default deny incoming      # deny imcoming connections
        sudo ufw default allow outgoing     # allow outgoing connections
        sudo ufw allow 2200\tcp             # allow listening on port 2200
        sudo ufw allow www                  # allow HTTP connections on port 80
        sudo ufw allow ntp                  # allow NTP connections on port 123
        sudo ufw show added                 # double check if all your changes are the ones you want 
        sudo ufw enable                     # enable the firewall
        sudo ufw status                     # it should give an Active status now
        ```
        
> *Warning:* When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. We already did this step in the very beginning so its all good! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.


Once done with those configurations, it is now time to create the `grader` user and give them root permission!


## Step 4: Create user Grader; Sudo! Sudo! Sudo!
    1. Run the following command to create the user:
        `sudo adduser grader`
    2. To give the grader user permission to sudo, run the following command:
        `sudo nano /etc/sudoers.d/grader`
    3. Inside the now open file, add the following text:
        "grader  ALL=(ALL:ALL) ALL"
    4. Save and Exit the file: Ctrl + X Y Enter`

That's it! Now your grader user has sudo permissions! It is now time to allow the grader to access our server!

## Step 5: Permission Granted!
    1. On your local machine, inside a terminal (Recommended for Windows: Git Bash), run the following command:
        `ssh-keygen`
    2. 