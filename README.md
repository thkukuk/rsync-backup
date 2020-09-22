# rsync-backup
Backup remote systems via rsync and ssh, store the data local via btrfs
snapshots and manage them with `snapper`.

## Introduction
`rsync-backup` collects data from remote hosts via rsync over ssh and stores them on a local btrfs partition via snapshots. `snapper` is used to create and manage the snapshots. This is space efficient, as only the changes are stored in a new snapshot and allows to manage a history of daily, weekly, monthly and yearly backups with automatic cleanup. Since the btrfs snapshots are read-only, they cannot be modified or destroyed by accident.

## How this works
On the backup server, for every client which should be backuped, a new btrfs subvolume will be created and `snapper` enabled for this.
A daily systemd timer job will run the backup in parallel up to a configured max. number (which could also be 1, so sequentiel).
The backuped data of a host will be stored in a subvolume, which contains already the data from before, so that only the changed data will be copied. After the backup finished, a new read-only snapshot of this data is created with `snapper`. As result the backup is immuteable and can not be changed by accident. From now `snapper` is responsible for the data and will delete automatically old backups keeping a configureable number of daily, weekly, monthly and yearly backups. Additional, it is possible to create diffs between different backups.

## Backup Server

### Requirements

You need a server with a disk big enough for all the backup data. Best is if
this machine is doing nothing else and not reacheable from outside and login
only possible via console. Thus the backups are save for virus like Emotet or
so.

Install the `server/rsync-backup` script and the `server/rsync-backup.conf`
confiuration file. Adjust the configuration file to your needs and paths on
your system. The default is, that backups are stored in `/backup` and the
client configuration files in `/etc/rsync-backup`.

I'm using a Raspberry Pi 4 with an external 2 TB harddisk and openSUSE MicroOS
as host OS to backup the configuration and data of my mail server and several
other machines.

### Adding a new client to backup

`<hostname>` is the name under which the client is reachable. If the backup
server itself should be backuped, the name is `localhost`. In this case, no
running ssh daemon is required.

Run ```rsync-backup init <hostname>```. A btrfs subvolume `/backup/<hostname`
will be created with a corresponding, adjusted snapper config file.

`snapper -c backup_<hostname> <command>` can be used to verify and adjust the
snapper configuration for this client backup or list all backups with the
date.

A configuration file `/etc/rsync-backup/<hostname.cfg>` is required. It
specifies, which directories on the client should be backuped, and allows to
specify additional options. The format is a simple `<directory> [optional rsync options]` format. Empty lines and comments will be ignored.

An example:
```
# Backup configuration, but no shadow passwords
/etc          --exclude=shadow
# Backup root directory, ignore .cache directory
/root         --exclude=.cache
# containers directory is volatile, don't backup /proc from chroots
/var/lib      --exclude=containers --exclude=proc
# Backup home directories, ignore .cache directories
/home         --exclude=.cache
```

### Backup one client

`rsync-backup sync <hostname>` will run a backup for the client `<hostname>`.
`snapper -c backup_<hostname> list` will afterwards show a new backup
including the additional size and date.


### Backup all clients

`rsync-backup sync-all` will backup all clients for which there is a
configuration file in `/etc/rsync-backukp`. The backup is done in parallel
according to the configuration. This command can be run daily by the
`rsync-backup.timer` systemd-timer service.
