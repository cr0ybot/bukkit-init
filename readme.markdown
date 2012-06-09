Initscript for minecraft/bukkit servers
=======================================
Place the minecraft file in /etc/init.d to start the minecraft server at boot.

This script places the world files on a ramdisk to speed up server read/write times. It also has a log-roll function to backup and clean the server.log, as a big logfile can slow down the server.


Requirements
------------
screen, rsync

Access server console
---------------------

	screen -r minecraft

Exit server console
-------------------

	Ctrl+A,D

Setup
=====

1. Move or symlink the script to `/etc/init.d/minecraft`, set the required premissions and update rc.d.

		chmod 755 /etc/init.d/minecraft
		update-rc.d minecraft defaults

2. Mount a ramdisk (I use `/home/minecraft/ramdisk`) or use the one premounted at `/dev/shm/`

3. Move your world dir to a new dir named `diskworld` and symlink `world` to the ramdisk instead.

		cd /home/minecraft/server
		mkdir diskworld
		mv world diskworld/world
		ln -s /home/minecraft/ramdisk/world world 

4. Create the backups directory

		cd /home/minecraft
		mkdir backups
		cd backups
		mkdir logs
		mkdir server
		mkdir world

5. Edit crontab to do a backup at 5am and copy the world from the ramdisk every 10 minutes

		sudo crontab -e

	and add these lines

		#m 	h 	dom	mon	dow	command
		05 	05 	*	*	*	/etc/init.d/minecraft backup
		00 	05 	*	*	*	/etc/init.d/minecraft log-roll
		*/10 	* 	*	*	*	/etc/init.d/minecraft to-disk

6. Make sure permissions on ALL files and folders in /home/minecraft are open to the `minecraft` user

		cd /home/minecraft/server
		ls -al

	for any file that does NOT say `minecraft` in the 3rd and 4th colums, do

		chown minecraft:minecraft [FILE NAME]

	and for any folder, do

		chown -R minecraft:minecraft [FOLDER NAME]

7. Edit the variables in the scriptfile to your needs
