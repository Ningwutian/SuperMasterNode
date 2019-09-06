
Debian
====================
This directory contains files used to package smnd/smn-qt
for Debian-based Linux systems. If you compile smnd/smn-qt yourself, there are some useful files here.

## smn: URI support ##


smn-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install smn-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your smn-qt binary to `/usr/bin`
and the `../../share/pixmaps/smn128.png` to `/usr/share/pixmaps`

smn-qt.protocol (KDE)

