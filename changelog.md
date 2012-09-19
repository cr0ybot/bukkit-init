## 1.3.0 (2012-09-18)

An almost total rewrite of the script.

 * New easy setup feature!
 * New commands:
   * setup
   * whitelist
   * whitelist-on
   * whitelist-off
   * whitelist-add
   * whitelist-remove
   * ops
   * op
   * deop
   * fix-permissions
 * New settings:
   * SERVERNAME
   * UPDATE_URL
   * WORLDNAME
   * USE_RAMDISK
 * Changed settings:
   * BACKUPPATH -> BACKUPWORLD
   * LOGPATH -> BACKUPLOG
   * WHOLEBACKUP -> BACKUPFULL
 * Prettier, more informative output (server name, service, and PID stated)
 * All necessary dirs are tested (and created or linked, if necessary) during runtime
 * Converted backtick syntax to newer bash $(...) syntax
 * Added exit status to places that needed it
 * Using ramdisk is now optional (but still highly recommended)
 * To-ram function now uses -u flag, so if files exist in RAM only files older than equivalent world storage files will be overwritten
 * Cron jobs for log-roll, backup, and full-backup won't run if server is not running (not necessary)
 * Changed backup compressed archives to gzip (slightly less compression but way faster than bz2)
 * Changed the way backup filename folders and dates are handled
 * Removed separate minecraft_server.jar and craftbukkit.jar updating
   * CraftBukkit is a total replacement, does not need original server file
   * Instead, update will download from the UPDATE_URL set in settings, so this can work for vanilla or CraftBukkit
 * Lots of comments added to code
 * Converted changelog to markdown format

## 1.2.1 (2012-08-06)

 * Split up update function since you might want to update only CraftBukkit or Minecraft
 * Changes to version command
 * Added some invocation options

## 1.2.0 (2012-07-08)

First release, based on Karrth's script (which was based on Athenus' script and the one found on the Minecraft Wiki).

 * Ramdisk files checked before overwriting with harddisk files (as opposed to automatically wiping and reverting to a backup 5-10 minutes old or older)
 * Fixes to update command
 * Set default user to `minecraft`
 * Changed some default directories
 * Added support for backing up The End
 