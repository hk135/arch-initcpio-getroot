Extra Initcpio Hooks to get the rootfs from a non-standard source

This repo came into being after my Media PC was modded while I was sleeping! The problem is that it is a front facing 
machine where locking it down would cause more trouble that it was worth. I wanted to find a way to reset the state
of the operating system to something that works easily and as a matter of course and so copying the operating system
down into memory at each boot seemed like the simplest way of achiving this.
The get_rootfs hooks should be BEFORE create_rootfs in the pipeline. This is because the rootfs has to be copied before
the create_rootfs hook finishes but after the rootfs has been created.

Depends on the core/mkinitcpio-nfs-utils package to provide the net hook

Example HOOKS line from mkinicpio.conf: HOOKS="base udev net get_rootfs_curltarball create_rootfs keyboard" 

Example pxelinux config:  append break initrd=initramfs.img ip=:::::eth0:dhcp create_rootfs_size=2200M curl_source=http://192.168.0.1/os/rootfs.tar.gz

Hooks:
### create_rootfs
This hook creates a tmpfs in the new root folder and then calls the value of get_root_handler (in a similar vein to 
mount_handler function already in initcpio)

CMDLINE options:
create_rootfs_type: tmpfs(default) or ramfs
create_rootfs_size: defaults to 1G, this is passed as -osize= when mounting the new rootfs


### get_rootfs_curltarball
This hook will download using curl and untar into the new root folder

CMDLINE options:
curl_source: REQUIRED source for curl
curl_opts: options to pass to curl, by default blank which produces a nice progress meter
tar_opts: options to pass to tar, by default -zxf which will untar without output (allowing for the curl progress meter)
	 when I added v to the opts I found I was waiting for the output to finish much longer than I would have otherwise

### get_rootfs_rsync
This hook will download using rsync and untar into the new root folder

CMDLINE options:

rsync_source: REQUIRED source for rsync
rsync_opts: options to pass to rsync, by default "-a --numeric-ids --progress"

Issues:
Couldn't get Symlinks to download as Symlinks increasing the memory footprint


