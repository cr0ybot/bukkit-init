Initscript for Minecraft servers on Ubuntu
==========================================
This is a script for starting a single-world Minecraft server at boot, and comes with a bunch of other features that make maintaining a server fairly simple.

Features
--------
+ Easy interactive setup feature. (NEW in 1.3.0)
+ Help command shows you all commands and their uses. (NEW in 1.3.0)
+ Whitelist commands (on, off, add, remove, and display). (NEW in 1.3.0)
+ Op commands (add, remove, and display). (NEW in 1.3.0)
+ Choose to use a ramdisk to speed up server read/write times to the world files. 
+ Specify max RAM the server process should use.
+ Stop, start, restart, and update the server (Minecraft & CraftBukkit) with simple console commands.
+ See who is currently connected, and all connects/disconnects since the last log-roll.
+ Easily push a message to people playing on the server.
+ Use Minecraft server commands without opening the server console screen.
+ Automatic backups using cron jobs.
+ Log-roll function to backup and clean the server.log, as a big logfile can slow down the server.

Requirements
------------
screen, rsync

Easy Setup
----------
New in version 1.3.0 is the ability to set up a Minecraft server in minutes!

First, install the script by placing it in `/etc/init.d/` as `minecraft` and run these commands:

		sudo chmod 755 /etc/init.d/minecraft
		sudo update-rc.d minecraft defaults

Then, run:

		sudo service minecraft setup

...and follow the on-screen instructions!

If you want full control over your setup, see "Manual Setup" below.

Manual Setup
------------
1. Edit the script's settings to your liking, then put it in `/etc/init.d/` as `minecraft`, set the required premissions, and set it to start at boot and stop at shutdown:

		sudo chmod 755 /etc/init.d/minecraft
		sudo update-rc.d minecraft defaults

2. Setup `minecraft` user and home directory:

		sudo useradd -m minecraft

3. Setup ramdisk (this script will not run without it):
	
	Create the ramdisk mount point:
	
		cd /home/minecraft
		sudo mkdir ramdisk
	
	Open `fstab` for editing with:

		sudo nano /etc/fstab

	...and add the line (tab deliniated):

		tmpfs	/home/minecraft/ramdisk	tmpfs	defaults,size=512m	0	0

	I'm setting my ramdisk up with 512MB, which is more than enough for my 43MB total world files; you may wish to use more or less. _Make sure you have enough RAM for this!_

	The ramdisk will mount every time you boot. If you wish to mount it immediately, use:

		sudo mount -t tmpfs none /home/minecraft/ramdisk -o size=512m

	...otherwise you must reboot now before continuing.

	See http://www.minecraftwiki.net/wiki/Tutorials/Ramdisk_enabled_server for more info on ramdisks.

4. If you haven't already, download CraftBukkit to `/home/minecraft/server` (or move it if you are applying this script to an existing server).

		sudo mkdir /home/minecraft/server
		cd /home/minecraft/server
		sudo wget 'http://dl.bukkit.org/latest-rb/craftbukkit.jar' -O craftbukkit.jar
	
5. If you already have a world generated (and it is located, along with the rest of you server files, in `/home/minecraft/server`), move your world directories into `/home/minecraft/server/world_storage`:

		cd /home/minecraft/server
		sudo mv world world_storage/world
		sudo mv world_nether world_storage/world_nether
		sudo mv world_the_end world_storage/world_the_end
		
	If your world folder has a unique name, like "Bobsworld", do this instead:
	
		sudo mv Bobsworld world_storage/Bobsworld
		sudo mv Bobsworld_nether world_storage/Bobsworld_nether
		sudo mv Bobsworld_the_end world_storage/Bobsworld_the_end

6. Make sure permissions on ALL files and folders in /home/minecraft are owned by the `minecraft` user:

		cd /home
		sudo chown -R minecraft:minecraft minecraft

	You can check permissions on all files within a folder by `cd`'ing into it and using:

		ls -al

7. Edit `crontab` to do a backup at 5am and copy the world from the ramdisk every 10 minutes:

		sudo crontab -e

	Add these lines (tab deliniated):

		#m 	h 	dom	mon	dow	command
		05 	05 	*	*	*	/etc/init.d/minecraft backup
		00 	05 	*	*	*	/etc/init.d/minecraft log-roll
		*/10 	* 	*	*	*	/etc/init.d/minecraft to-disk

And that _should_ be all there is to it. Please file an Issue if you have trouble with this setup.

Usage
-----
The script starts your server at boot and, if you added the cron jobs, will backup your worlds every day.

You can issue a command to the script by calling:

		sudo service minecraft [COMMAND]

__Available commands:__
+ setup					Interactive prompt for setting up the server with this script
+ start					Starts the server
+ stop					Stops the server
+ restart				Restarts the server
+ backup				Backups the worlds defined in the script
+ full-backup			Backups the entire server folder
+ to-disk				Copies the world from the ramdisk to world_storage
+ update				Fetches the latest version of $SERVICE
+ log-roll				Moves and gzips the logfile
+ say					Prints the given string to the in-game chat
+ command				Executes a command in-game
+ connected				Lists connected users
+ recent				Displays recently connected users
+ whitelist				Displays server whitelist
+ whitelist-on			Tells server to use whitelist (server open to only those players listed in whitelist).
+ whitelist-off			Tells server to NOT use whitelist (server open to all players).
+ whitelist-add NAME	Adds the specified player to the server whitelist
+ whitelist-remove NAME	Removes the specified player from the server whitelist
+ ops					Displays server ops
+ op NAME				Grants NAME operator status.
+ deop NAME				Revokes NAME's operator status.
+ console				Displays the server console screen, exit with Ctrl+A, D
+ status				Displays server status
+ version				Displays $SERVICE version and then exits
+ fix-permissions		Sets ownership of all files in /home/$USERNAME to $USERNAME
+ help					Displays this list of commands

To Do List
----------
+ Autodetect WORLDNAME
+ Provide commands for restoring backups
+ Remove backups older than a specified time period


`Tested on Ubuntu 12.04.1 server; may work on other flavors of Debian`
