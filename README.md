https://jakub.kotrla.net/putty/

# PuTTY for win32 storing configuration into file (*partial information from author*)

##  Information

PuTTY is a great free telnet/ssh client for Win32 and Unix platforms. It's just one "exe", no install, no extra dlls are required.
So it can be run from floppy, usb flash disk etc.

But it stores all configuration into registry ([HKEY\_CURRENT\_USER\Software\SimonTatham\PuTTY]) - you cannot store/load it from file on the floppy or other removable devices.
There is a [workaround with bat/reg files](http://the.earth.li/~sgtatham/putty/0.58/htmldoc/Chapter4.html#config-file)  but that isn't very nice. So I've rewritten small part of PuTTY (winstore.c - functions which handle storing/loading configuration).

I've rewritten small part of Pageant too, to be able to load list of stored session (winpgnt.c - function update\_sessions).

If you find a bug related to storing/loading configuration or not present in original PuTTY feel free to e-mail it to jakub@kotrla.net.
My modification to PuTTY is under same [licence as PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/licence.html) - you can do anything with it but at your own risk and without any warranty.

## Documentation

This PuTTY stores its configuration (sessions, ssh host keys, random seed file path) to file instead of registry.
Every session and ssh host key is stored in a separate file. Default paths are (where . represents executable directory):

    * ./sessions/packedSessionName
    * ./sshhostkeys/packedHostName
    * ./putty.rnd

Path for saving configuration can be set via file putty.conf. Current working
directory is searched first, if putty.conf is not found there, executable
directory (same directory as putty/pscp/psftp/plink/pageant.exe) is searched.
putty.conf should look like this (if it's not found defaults are used):

		;comment line
		sessions=%SYSTEMROOT%\ses
		sshhostkeys=\ssh\hostkeys
		seedfile=C:\putty.rnd
		sessionsuffix=.session
		keysuffix=.hostkey
		jumplist=jumplist.txt
	

You can use enviroment variables in config (like %SYSTEMROOT%) - string will be expanded via ExpandEnviromentString WinAPI function (user-specific variables are not supported yet).

sessionsuffix and keysuffix are optional, defaults are empty. If set, every file has a suffix as defined (saved sessions via sessionsuffix and ssh host keys via keysuffix).
Primary purpose is to avoid "\*.com" files from names like ssh.domain.com. Both are limited to 15 characters.
Warning: if you already have saved some sessions or ssh host keys and you change these suffixes, you have to manually rename (append them to) all files.

Jumplist is new feature in Windows 7 supported by PuTTY 0.61. Because this PuTTY should be lightweight, if you do not set path to jumplist, none will be created.

This PuTTY is still able to load configuration from registry. Sessions loaded from registry are marked [registry].
When PuTTY is checking ssh host key and it's not found in file but in registry, you can move/copy key to file (or of course do nothing).

Pageant loads list of saved sessions from path set in putty.conf, defaults is ./sessions/packedSessionName - it works the same way as PuTTY (including keysuffix setting). 

