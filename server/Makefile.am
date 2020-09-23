
systemdsystemunit_DATA = systemd/rsync-backup.timer \
			systemd/rsync-backup.service

sbin_SCRIPTS = rsync-backup

man_MANS = rsync-backup.1

if HAVE_VENDORDIR
configdir=@vendordir@
else
configdir=${sysconfdir}
endif

config_DATA = rsync-backup.conf

EXTRA_DIST = $(DATA) $(SCRIPTS) $(MANS) rsync-backup.1.md \
	systemd/rsync-backup.service.in

%.1: %.1.md
	pandoc -s -f markdown -t man $< --output $@
