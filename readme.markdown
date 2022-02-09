Init script for minecraft/bukkit servers
=======================================
A init script that apart from starting and stopping the server correctly also has some extra features
for running a mincraft/craftbukkit server.

Features
--------

 * Utilization of ramdisk for world data, decreases lag when getting world chunks
 * Cleaning of server.log, a big log file slows down the server
 * Backup for worlds
 * Server updating and complete backup
 * Exclude files and directories from full backup by adding them to "exclude.list"

Requirements
------------
screen, rsync

Access server console
=====================

	screen -r minecraft

Exit the console
	
	Ctrl+A D


Editing config
=====

Depending on your server and file system configuration change the variables according to comments.

IMPORTANT:
  - by default script working for separate user called minecraft; if you want to make this user as a system user change line 44 in `minecraft`
    from `su $USERNAME -s /bin/bash -c "$1"` on `sudo -u $USERNAME -s /bin/bash -c "$1"`
    
  - if you have error Rogue pidfile found at the startup check if `INVOCATION` string from `config.example` file works when you call it manually (don't forget replace variables on your actual values ex. `-Xmx$MAXMEM` -> `-Xmx2048M`). Some parameters could be not working with your java so just remove them.
  - In my case on version `openjdk-17-jre-headless` and `craftbukkit1.18.0` my `INVOCATION` string looks like that `INVOCATION="java -Xmx$MAXMEM -Xms$INITMEM -XX:ParallelGCThreads=$CPU_COUNT -jar craftbukkit.jar nogui"`


Setup
=====

1. Symlink the minecraft file to `/etc/init.d/minecraft`, set the required premissions and update rc.d.

		sudo ln -s ~/minecraft-init/minecraft /etc/init.d/minecraft
		chmod 755  ~/minecraft-init/minecraft
		sudo update-rc.d minecraft defaults

2. Edit the variables in `config.example` **(SEE EDITING CONFIG)** to your needs and rename it to `config` (leaving it in the same folder as the original minecraft script)

3. Move your worlds to the folder specified by `WORLDSTORAGE`

4. Edit crontab

	As the server user:
	
		crontab -e

	Add these lines:

		#m 	h 	dom	mon	dow	command
		02 	05 	*	*	*	/etc/init.d/minecraft backup
		55 	04 	*	*	*	/etc/init.d/minecraft log-roll
		*/30 	* 	*	*	*	/etc/init.d/minecraft to-disk

5. To run the server run:

		/etc/init.d/minecraft start

6. (Optional) To load a world from ramdisk run:

		/etc/init.d/minecraft ramdisk WORLDNAME
	
	to disable ramdisk, run the same command again.


For more help with the script, run

	/etc/init.d/minecraft help

[![Flattr this git repo](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=Ahtenus&url=https://github.com/Ahtenus/minecraft-init&title=minecraft-init&language=en_GB&tags=github&category=software) 

Good stuff
==========
[Backup rotation script](https://github.com/adamfeuer/rotate-backups) good if you want some kind or rolling of the world backups.
