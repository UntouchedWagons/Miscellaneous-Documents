# Automount of CIFS (smbfs) folders w/ systemd
## i.e. mounting your Windows shares on Linux at boot

First, let's see how to mount the remote directory. Assume that there is a shared folder over the network at `\\192.168.1.1\users\self\shared` which is accessible with user `myuser` and password `secret123`.

We could mount it manually in `/mnt/winshare` with:

	# mount -t cifs //192.168.1.1/users/self/shared /mnt/winshare -o user=myuser,password=secret123

This should work on your Linux box, because systemd will basically call mount with the same arguments: `What` (`//192.168.1.1/users/self/shared`), `Where` (`/mnt/winshare`) and `Options` (`user=myuser,password=secret123`).

To configure systemd to automatically mount that network folder on boot, there are 2 files needed: `mnt-winshare.mount` and `mnt-winshare.automount`.
The `.mount` unit file specifies how to mount a drive (`man systemd.mount` for details), while the `.automount` unit file specifies what to mount automatically on boot (`man systemd.automount`).

**Important!** Note that the name of the file **must** map to the actual filesystem mount target, that is `mnt-winshare.(auto)mount` refers to `/mnt/winshare`. E.g. to mount in `/home/user/myfolder` the file names must be `home-user-myfolder.(auto)mount`.

The content of the files is the following:

	# cat /etc/systemd/system/mnt-winshare.mount
	[Unit]
	Description=Remote Win Mount
	
	[Mount]
	What=//192.168.1.1/users/self/shared
	Where=/mnt/winshare
	Type=cifs
	Options=user=myuser,password=secret123,uid=1000,gid=1000
	
	[Install]
	WantedBy=multi-user.target

and
	
	# cat /etc/systemd/system/mnt-winshare.automount
	[Unit]
	Description=Automount Remote Win Mount
	
	[Automount]
	Where=/mnt/winshare
	
	[Install]
	WantedBy=multi-user.target

The former file goes in `/etc/systemd/system/mnt-winshare.mount` while the latter `/etc/systemd/system/mnt-winshare.automount`.

Then, reload the units to ensure everything is to its latest version:

	# systemctl daemon-reload

At this point, we should be able to manually mount and unmount the remote folder using `systemctl`:

	# systemctl start mnt-winshare.mount
	# systemctl stop mnt-winshare.mount

This will not, however, automatically mount the system at startup. To do so, just enable the automount

	# systemctl enable mnt-winshare.automount

And this should be it!

## Source

https://gist.github.com/akiross/1aa81f67514ef4753f2c8a15040364a3
