
sudoersddir=@sysconfdir@/sudoers.d
sshtemplatedir=$(pkgdatadir)/.ssh

libexec_SCRIPTS=rsync-backup-client

tmpfiles_DATA=rsync-backup-home.conf
sysusers_DATA=rsync-backup-user.conf
sudoersd_DATA=rsync-backup-sudoers
sshtemplate_DATA=authorized_keys.template

EXTRA_DIST=${SCRIPTS} ${DATA} authorized_keys.template.in
