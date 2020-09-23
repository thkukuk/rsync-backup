% RSYNC-BACKUP(1)
% Thorsten Kukuk `<kukuk@suse.com>`

# NAME

rsync-backup - Remote backup with rsync and ssh to a btrfs subvolume

# SYNOPSIS

rsync-backup *command* [*hostname* ...]

# DESCRIPTION

`rsync-backup` collects data from remote hosts via rsync over ssh and stores
them on a local btrfs partition via snapshots. `snapper(8)` is used to create
and manage the snapshots. This is space efficient, as only the changes are
stored in a new snapshot and allows to manage a history of daily, weekly,
monthly and yearly backups with automatic cleanup. Since the btrfs snapshots
are read-only, they cannot be modified or destroyed by accident.

# OPTIONS

init *hostname* [...]
:    Initialize btrfs subvolume for new backup client

sync *hostname* [...]
:    Run backup for all listed clients

sync-all
:    Run backup in parallel for all clients

# CONFIGURATION FILES

/usr/etc/rsync-backup.conf
:  Vendor provided configuration file, contains the defaults.

/etc/rsync-backup.conf
:  Admin provided configuration file, should only contain the variables which
were changed by the system administrator compared to the vendor configuration
file.

/etc/rsync-backup/\<hostname.cfg\>
:  This configuration file specifies, which directories on the client should be
backuped, and allows to specify additional options. The format is a simple
"\<directory\> [optional rsync options]" format. Empty lines and comments will
be ignored.
