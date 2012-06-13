Initscript for Minecraft+CraftBukkit servers on Ubuntu
=================================================
This is a script for starting a single-world Minecraft+CraftBukkit server at boot, and comes with a bunch of other features that make maintaining a server fairly simple.

Features
--------
+ Uses a ramdisk to speed up server read/write times to the world files. 
+ Automatic backups using cron jobs.
+ Log-roll function to backup and clean the server.log, as a big logfile can slow down the server.
+ Specify max RAM the server process should use.
+ Stop, start, restart, and update the server (Minecraft & CraftBukkit) with simple console commands.
+ See who is currently connected, and all connects/disconnects since the last log-roll.
+ Easily push a message to people playing on the server.
+ Use Minecraft server commands without opening the server console screen.

Requirements
------------
screen, rsync

Setup
-----
1. Put this script in `/etc/init.d/minecraft`, set the required premissions, and update rc.d:

		sudo chmod 755 /etc/init.d/minecraft
		sudo update-rc.d minecraft defaults

2. Setup `minecraft` user and home directory:

		sudo useradd -m minecraft
		cd /home/minecraft
		sudo mkdir -p {backups/{logs,server,world},ramdisk,server/diskworld}

3. Setup ramdisk:

	Open `fstab` for editing with:

		sudo nano /etc/fstab

	...and add the line (tab deliniated):

		tmpfs	/home/minecraft/ramdisk	tmpfs	defaults,size=2048m	0	0

	I'm setting my ramdisk up with a full 2GB; you may wish to use more or less. _Make sure you have enough RAM for this!_

	The ramdisk will mount every time you boot. If you wish to mount it immediately, use:

		sudo mount -t tmpfs none /home/minecraft/ramdisk -o size=2048m

	...otherwise you must reboot now before continuing.

	See http://www.minecraftwiki.net/wiki/Tutorials/Ramdisk_enabled_server for more info on ramdisks.

4. If you already have a world generated (and it is located, along with the rest of you server files, in `/home/minecraft/server`), move your world dir into `diskworld`:

		cd /home/minecraft/server
		mv world diskworld/world

5. Set up symbolic links for your minecraft world to be automatically stored on the ramdisk:

		cd /home/minecraft/server
		sudo ln -s /home/minecraft/ramdisk/world world
		sudo ln -s /home/minecraft/ramdisk/world_nether world_nether
		sudo ln -s /home/minecraft/ramdisk/world_the_end world_the_end

6. Make sure permissions on ALL files and folders in /home/minecraft are owned by the `minecraft` user:

		cd /home
		sudo chown -R minecraft:minecraft minecraft

	You can check permissions on all files within a folder by `cd`'ing into it and using:

		ls -al

7. If you haven't already, download the_ minecraft_server.jar_ and CraftBukkit (rename to _craftbukkit.jar_) (or move them if you are applying this script to an existing server) and place them in `/home/minecraft/server`.

8. Edit `crontab` to do a backup at 5am and copy the world from the ramdisk every 10 minutes:

		sudo crontab -e

	Add these lines:

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

Available commands:
+ start - _starts the server_
+ stop - _stops the server_
+ restart - _restarts the server_
+ backup - _backs up your Minecraft world to the `backups/world` directory_
+ whole-backup - _backs up the entire `server` folder to `backups/server`_
+ update - _attempts to update_ minecraft.jar _and_ craftbukkit.jar
+ to-disk - _copies the world files from the ramdisk to the harddisk, so that if the server crashes or reboots, the files can be transferred back to the ramdisk (RAM does not hold data when power is off)_
+ connected - _lists currently connected playeres_
+ last - _lists all connect/disconnect activity since the last log-roll_
+ console - _brings up the Minecraft server console using_ screen _- exit console with Ctrl+A, D_
+ say - _expects input: displays a message to users connected to the server, eg:_ `sudo service minecraft say "Hello world!"`
+ command - _expects input: executes a Minecraft server command, see_ http://www.minecraftwiki.net/wiki/SMP_Server_commands
+ log-roll - _"rolls" the server.log file, wiping it clean and storing the old log in `backups/logs`_
+ status - _check if server is running or not_
+ version - _displays version of server (needs updating since changing log-roll function)_


`Tested on Ubuntu 12.04 server; may work on Debian`
