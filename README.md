# rsync-backup
Backup remote systems via rsync and ssh and store the data local via btrfs snapshots

## Introduction
`rsync-backup` collects data from remote hosts via rsync over ssh and stores them on a local btrfs partition via snapshots. `snapper` is used to create and manage the snapshots. This is space efficient, as only the changes are stored in a new snapshot and allows to manage a history of daily, weekly, monthly and yearly backups with automatic cleanup.

## How this works
On the backup server, for every client which should be backuped, a new btrfs subvolume will be created and `snapper` enabled for this. 
A daily systemd timer job will run the backup in parallel up to a configured max. number (which could also be 1, so sequentiel).
The backuped data of a host will be stored in a subvolume, which contains already the data from the day before, so that only the changed data will be copied. After the backup finished, a new read-only snapshot of this data is created with `snapper`. Which means, that the backup is immuteable and can not be changed by accident. From now `snapper` is responsible for the data and will delete automatically old backups keeping a configureable number of daily, weekly, monthly and yearly backups. Additional, it is possible to create diffs between different backups.
