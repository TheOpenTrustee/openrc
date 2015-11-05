# OpenRC NEWS

This file will contain a list of notable changes for each release. Note
the information in this file is in reverse order.

## OpenRC-0.19

This version adds a net-online service. By default, this
service will check all known network interfaces for a configured
interface or a carrier. It will register as started only when all
interfaces are configured and there is at least a carrier on one
interface. The behaviour of this service can be modified in
/etc/conf.d/net-online.

Currently, this only works on Linux, but if anyone wants to port to
*bsd, that would be welcomed.

## OpenRC-0.18.3

Modern Linux systems expect /etc/mtab to be a symbolic link to
/proc/self/mounts. Reasons for this change include support for mount
namespaces, which will not work if /etc/mtab is a file.
By default, the mtab service enforces this on each reboot.

If you find that this breaks your system in some way, please do the
following:

- Set mtab_is_file=yes in /etc/conf.d/mtab.

- Restart mtab. This will recreate the /etc/mtab file.

- Check for an issue on https://github.com/openrc/openrc/issues
  explaining why you need /etc/mtab to be a file. If there isn't one,
  please open one and explain in detail why you need this to be a file.
  If there is one, please add your comments to it. Please give concrete
  examples of why  it is important that /etc/mtab be a file instead of a
  symbolic link. Those comments will be taken into consideration for how
  long to keep supporting mtab as a file or when the support can be
  removed.

## OpenRC-0.18

The behaviour of localmount and netmount in this version is changing. In
the past, these services always started successfully. In this version,
they will be able to fail if file systems they mount fail to mount. If
you have file systems listed in fstab which should not be mounted at
boot time, make sure to add noauto to the mount options. If you have
file systems that you want to attempt to mount at boot time but failure
should be allowed, add nofail to the mount options for these file
systems in fstab.

## OpenRC-0.14

The binfmt service, which registers misc binary formats with the Linux
kernel, has been separated from the procfs service. This service will be
automatically added to the boot runlevel for new Linux installs. When
you upgrade, you will need to use rc-update to add it to your boot
runlevel.

The procfs service no longer automounts the deprecated usbfs and
usbdevfs file systems. Nothing should be using usbdevfs any longer, and
if you still need usbfs it can be added to fstab.

Related to the above change, the procfs service no longer attempts to
modprobe the usbcore module. If your device manager does not load it,
you will need to configure the modules service to do so.

The override order of binfmt.d and tmpfiles.d directories has been
changed to match systemd. Files in /run/binfmt.d and /run/tmpfiles.d
override their /usr/lib counterparts, and files in the /etc counterparts
override both /usr/lib and /run.

## OpenRC-0.13.2

A chroot variable has been added to the service script variables.
This fixes the support for running a service in a chroot.
This is documented in man 8 openrc-run.

The netmount service now mounts nfs file systems.
This change was made to correct a fix for an earlier bug.

## OpenRC-0.13

/sbin/rc was renamed to /sbin/openrc and /sbin/runscript was renamed to
/sbin/openrc-run due to naming conflicts with other software.

Backward compatible symbolic links are currently in place so your
system will keep working if you are using the old names; however, it is
strongly advised that you migrate to the new names because the symbolic
links will be removed in the future.
Warnings have been added to assist with this migration; however, due to the
level of noise they produce, they only appear in verbose mode in this release.

The devfs script now handles the initial mounting and setup of the
/dev directory. If /dev has already been mounted by the kernel or an
initramfs, devfs will remount /dev with the correct mount options
instead of mounting a second /dev over the existing mount point.

It attempts to mount /dev from fstab first if an entry exists there. If
it doesn't it attempts to mount devtmpfs if it is configured in the
kernel. If not, it attempts to mount tmpfs.
If none of these is available, an error message is displayed and static
/dev is assumed.

## OpenRC-0.12

The net.* scripts, originally from Gentoo Linux, have
been removed. If you need these scripts, look for a package called
netifrc, which is maintained by them.