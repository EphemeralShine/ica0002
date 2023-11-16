# Install and configure infrastructure with Ansible:
```
ansible-playbook infra.yaml
```
# Restore MySQL data from the backup:

- Restore backups from remote server
```
sudo -u backup duplicity --no-encryption restore rsync://EphemeralShine@backup/./mysql /home/backup/restore/mysql
```
Only incase this error occurs: 
```
Restore destination directory /home/backup/restore/mysql already exists.
Will not overwrite.
```
Run this command:
```
sudo rm -rf /home/backup/restore/mysql
```
- Become root user
```
sudo su -
```
- Copy data into agama database
```
mysql agama < /home/backup/restore/mysql/agama.sql
```
- Clean up
```
sudo rm -rf /home/backup/restore/mysql
```
# Restore InfluxDB data from the backup:
- Restore backups from remote server
```
sudo -u backup duplicity --no-encryption restore rsync://EphemeralShine@backup/./influxdb /home/backup/restore/influxdb
```
Only incase this error occurs: 
```
Restore destination directory /home/backup/restore/influxdb already exists.
Will not overwrite.
```
Run this command:
```
sudo rm -rf /home/backup/restore/influxdb
```
And retry the first  command.
- Stop telegraf service
```
sudo service telegraf stop
```
- Delete old database
```
influx -execute 'DROP DATABASE telegraf'
```
- Restore from the backup file
```
influxdb restore -portable -database telegraf /home/backup/restore/influxdb
```
- Clean up
```
sudo rm -rf /home/backup/restore/influxdb
```
- Restart telegraf service 
```
ansible-playbook infra.yaml -ti
```
# Verify results
- Mysql
```
sudo mysql -e "SELECT * FROM agama.item"
```
You should see table data in the output result in this format:
```
+----+-------+-------+
| id | value | state |
+----+-------+-------+
|  4 | Five  |     0 |
|  5 | three |     0 |
|  6 | two   |     0 |
|  7 | ONE   |     0 |
+----+-------+-------+
```
- Influxdb 
```
influx -execute 'SELECT * FROM "syslog" LIMIT 3' -database="telegraf"
```
Yous should see a wall of text
