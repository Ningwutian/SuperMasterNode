Sample init scripts and service configuration for smnd
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/smnd.service:    systemd service unit configuration
    contrib/init/smnd.openrc:     OpenRC compatible SysV style init script
    contrib/init/smnd.openrcconf: OpenRC conf.d file
    contrib/init/smnd.conf:       Upstart service configuration file
    contrib/init/smnd.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "smn" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes smnd will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, smnd requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, smnd will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that smnd and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If smnd is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running smnd without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/smn.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/smnd`  
Configuration file:  `/etc/smn/smn.conf`  
Data directory:      `/var/lib/smnd`  
PID file:            `/var/run/smnd/smnd.pid` (OpenRC and Upstart) or `/var/lib/smnd/smnd.pid` (systemd)  
Lock file:           `/var/lock/subsys/smnd` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the smn user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
smn user and group.  Access to smn-cli and other smnd rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/smnd`  
Configuration file:  `~/Library/Application Support/SMN/smn.conf`  
Data directory:      `~/Library/Application Support/SMN`
Lock file:           `~/Library/Application Support/SMN/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start smnd` and to enable for system startup run
`systemctl enable smnd`

4b) OpenRC

Rename smnd.openrc to smnd and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/smnd start` and configure it to run on startup with
`rc-update add smnd`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop smnd.conf in /etc/init.  Test by running `service smnd start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy smnd.init to /etc/init.d/smnd. Test by running `service smnd start`.

Using this script, you can adjust the path and flags to the smnd program by
setting the SMND and FLAGS environment variables in the file
/etc/sysconfig/smnd. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.smn.smnd.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.smn.smnd.plist`.

This Launch Agent will cause smnd to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run smnd as the current user.
You will need to modify org.smn.smnd.plist if you intend to use it as a
Launch Daemon with a dedicated smn user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
