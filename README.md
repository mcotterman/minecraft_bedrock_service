# Minecraft Bedrock Server Linux Service Script
By: Mike Cotterman
Licensed under MIT License - AS IS
See Overview for credit to original script author

## Table of Contents
* [Overview](#overview)
* [Important Notes](#important-notes)
* [Prereqs](#prereqs)
* [Tested Configurations](#tested-configurations)
* [Usage](#usage)
* [Installation Steps](#installation-steps)

## Overview
This script provides you with the ability to autostart, start, stop, update, run commands and backup your Minecraft Bedrock server service on a Linux server.

This script is a modification and heavily based on the Minecraft Java Edition script found here:
https://minecraft.gamepedia.com/Tutorials/Server_startup_script

## Important Notes
* When an update is run files not specified in the `UPDATESKIPS` variable of the script that are in the update will be overwritten!
* Do NOT put spaces in your MCPATH variable

## Prereqs
You should have a server built with the following requirements.
* Ubuntu Linux 18.04 (tested on this version)
* Ensure the following software is installed:
  * wget - `sudo apt install wget`
  * unzip - `sudo apt install unzip`

## Tested Configurations
### Ubuntu VM running on ESXi
* Ubuntu Linux 18.04
* Bedrrock Server 1.16
* 2GB RAM
* 1 vCPU

## Usage
`sudo service mcbedrock {start|stop|update|backup|status|restart|command "server command"`
* `start` - Start the server
* `stop` - Stop the server
* `update` - Check for updates. If they are available, download and install them
   * **Caution** Files not specified in the `UPDATESKIPS` variable of the script that are in the update will be overwritten!
   * You MUST have a `versions` file created as defined in the Installation Steps
* `backup` - Backup the system config and world files desognated by the `WORLD` variable of the script
* `status` - Display the status of the service
* `restart` - Stop and start the service
* `command "some server_command"` - Inject server commends into the console
  * Ex: `sudo service mcbedrock command "op someUser"`

## Installation Steps
1. Ensure that you have Ubuntu Linux installed
1. Create a new standard user named **minecraft** - `sudo useradd -m -s /bin/bash minecraft`
1. Ensure **wget** is installed - `sudo apt -y install wget`
1. Ensure **unzip** is installed - `sudo apt -y install unzip`
1. Create a new folder to store your server software (Example:)
    1. `sudo mkdir -p /usr/games/minecraft-bedrock`
    2. `sudo chown -R minecraft /usr/games/minecraft-bedrock`
1. Install Minecraft Bedrock Server *(I'm considering adding this part to the script)*
    * (Download the server software)[]
    1. [Visit the Mincraft Bedrock Server download page](https://www.minecraft.net/en-us/download/server/bedrock/)
    2. Check the **I agree** box (after carefully reading and considering the EULA of course)
    3. Right click the **Download** button and select **Copy link address** and paste it into notepad for later reference
    4. SSH to your linux server and run `sudo -i -u minecraft` to switch to your minecraft user
    5. Change to your minecraft bedrock server folder - `cd /usr/games/minecraft-bedrock`
    6. Create a file called **version** and paste the URL from step 3 into it
        * Run `vi version`
        * Press Escape, then press **i**
        * Paste the URL from step 3
        * Press Escape then `:wq` and press enter
    6. Download the server software - `wget -O mc-server.zip $(< version)`
    7. Unzip the server software - `unzip mc-server.zip`
    8. Make any needed changes to system.properties using your favorite text editor
        * The file is pretty well documented, but if you need clarification Google around and you should find what you need
    9. Exit your **minecraft** user - `exit`
1. Grab the server service script
    * `sudo wget -O /etc/init.d/mcbedrock https://raw.githubusercontent.com/mcotterman/minecraft_bedrock_service/master/mcbedrock`
1. Make script executable - `sudo chmod +x /etc/init.d/mcbedrock`
1. Edit the script with your favorite text editor and update
    * **WORLD** variable - The name of the worled you have as the **level-name** option in server.properties
    * **MCPATH** variable - The path to your bedrock server (Default: /usr/games/minecraft-bedrock)
    * **UPDATESKIPS** variable - Add more items to this space delimited list if you do not want certian files overwritten during updates
    * **USERNAME** - The username of the user you wish to run the Minecraft Bedrock server (Default: minecraft)
1. Enable the script
    * `sudo /lib/systemd/systemd-sysv-install enable mcbedrock` 
1. Set startup and shutdown defaults
    * `sudoupdate-rc.d minecraft defaults`
1. Try it out!
    * `sudo service mcbedrock start`
