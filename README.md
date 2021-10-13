# ansible-playbook-rpi-router

Playbook to setup a simple router on a Raspberry Pi. This router will stand in
between my actual router and my dumb HP printer because the HP printer's
inbuilt WiFi is absolute shit and keeps disconnecting randomly all the time.
Using the Pi's WiFi, I can expect a much better printing experience

The Pi will be using a read only rootfs with a tmpfs writeable layer on top of
it using overlayfs. This should help the Pi be much more resistant against SD
card / USB drive corruption issues.

This playbook only works with Raspberry Pi OS.

Need to set the following variables in vars.yml:

| Variable | Description |
| --- | --- |
| `user_name` | Main user's username |
| `user_email` | Main user's email address|
| `user_comment` | Main user's `/etc/passwd` comment field entry |
| `user_password_hash` | Main user's password hash |
| `user_ssh_key` | Main user's public SSH key |
| `host_name` | Pi's hostname |
| `host_domain` | Pi's domain name |
| `time_zone_file` | `Path to the time zone file under /usr/share/zoneinfo` |

## Instructions

This playbook needs to be run in 2 passes. The first pass will setup all the
necessary packages and services on the Raspberry Pi and the second pass will
configure the Pi to use overlayfs as the root filesystem

Install `ansible` and `git` on your Pi by running:

```
apt-get install --no-install-recommends --no-install-suggests ansible git
```

Then download this playbook using git-clone:

```
git clone https://github.com/rpthms/ansible-playbook-rpi-router.git
```

### Pass 1

Start the first pass by running `ansible-playbook site-before-reboot.yml`. The
first pass takes care of the following:

* `apt`: Remove unnecessary packages and upgrade the system
* `system-config`: Do some basic system configuration like setting the hostname, locale etc.
* `user-config`: Setup a main user and disable the default 'pi' user
* `networking`: Configure systemd-networkd and wpa_supplicant
* `dhcpcd`: Start a DHCP server that listens on the ethernet port
* `systemd-resolved`: Configure and enable systemd-resolved

Reboot the system after completing Pass 1.

### Pass 2

Start the second pass by running `ansible-playbook site-after-reboot.yml`. The
first pass takes care of the following:

* `overlayfs`: Use an overlayfs mount for the root filesystem to avoid writes
on the SD card / USB drive.

Reboot the system after completing Pass 2.

### After running the playbook

**N.B**: Do these steps before rebooting your Pi after completing Pass 2,
because after rebooting you can no longer make changes to any files on the root
filesystem. (You'll have to load your SD card / USB drive on a PC to make any
changes)

Once the playbook has finished its run, you can do a couple more optional things:

* Uninstall packages that couldn't be uninstalled before: `apt purge ansible git dhcpcd5`
* Clear out files from `pi`'s home directory
* Delete all log files from `/var/log`
* Delete apt package archives and list in `/var/cache/apt/archives` and `/var/lib/apt/lists`
