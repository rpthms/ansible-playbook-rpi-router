# ansible-playbook-rpi-router

Playbook to setup a simple router on a Raspberry Pi. This router will stand in
between my actual router and my dumb HP printer because the HP printer's
inbuilt WiFi is absolute shit and keeps disconnecting randomly all the time.
Using the Pi's WiFi, I can expect a much better printing experience

The Pi will be using a read only rootfs with a tmpfs writeable layer on top of
it using overlayfs. This should help the Pi be much more resistant against SD
card / USB drive corruption issues.

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

## After running the playbook

**N.B**: Do these steps before rebooting your Pi, because after rebooting you
can no longer make changes to any file on the root filesystem. (You'll have to
load your SD card / USB drive on a PC to make any changes)

Once the playbook has finished its run, you can do a couple more optional things:

* Uninstall ansible: `apt purge ansible`
* Clear out files from `pi`'s home directory
* Delete all log files from `/var/log`
