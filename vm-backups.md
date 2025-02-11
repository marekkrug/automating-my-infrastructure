## VM Backups erstellen
```bash
apt install borgbackup borgmatic
 ssh-keygen -t ed25519 -a 200 -f /root/.ssh/id_ed25519
cat /root/.ssh/id_ed25519.pub | ssh -p 23 u446949@u446949.your-storagebox.de install-ssh-key
ssh -i /root/.ssh/id_ed25519 u446949-sub1@u446949.your-storagebox.de -p23
```

### generate-borgmatic-config
```bash
vi /etc/borgmatic/config.yaml
```

```yml
	location:
		source_directories:
			- /home
			- /etc
			- /root
			- /var/log
			- /opt
		
	repositories:
		 - ssh://u446949-sub1@u446949.your-storagebox.de:23/home/borg

	storage:
		encryption_passphrase: "...." 
		ssh_command: ssh -i /root/.ssh/id_ed25519
	retention:
		keep_hourly: 24
		keep_daily: 7
		keep_weekly: 4
		keep_monthly: 12
	hooks:
		before_backup:
			- echo "Starting a backup job."
		after_backup:
			- echo "Backup created."
		on_error:
			- echo "Error while creating a backup."
```
### validate-borgmatic-config
```bash
borgmatic init --encryption repokey-blake2
```

```bash
crontab -e 
```

```yaml
	0 * * * * /usr/bin/borgmatic --stats 2>&1
```