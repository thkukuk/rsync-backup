
systemdsystemunit_DATA = systemd/rsync-backup.timer \
			systemd/rsync-backup.service

sbin_SCRIPTS = rsync-backup

if HAVE_VENDORDIR
configdir=@vendordir@
else
configdir=${sysconfdir}
endif

config_DATA = rsync-backup.conf

EXTRA_DIST = $(DATA) $(SCRIPTS) systemd/rsync-backup.service.in
