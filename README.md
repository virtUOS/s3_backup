# Ansible Role for Backups to S3 Buckets using restic

This ansible script provides a simple way of confguring automatic backups to
an S3 bucket with restic.

## Restic
A backup tool that utilizes deduplication to minimize storage requirements for backups. It saves snapshots (backups) to an
encrypted repository on the s3 bucket.

Listing all snapshots: 
- Load the credentials saved in the environment variables using `source /opt/backup/restic` 
- use `restic snapshots`to list all snapshots

Restoring from backup:
- Load the credentials saved in the environment variables using `source /opt/backup/restic` 
- use `sudo -E restic restore [snapshot name] --target [local path]` to copy the files saved by that snapshot to the path

It is also possible to restore/exclude/include specific files/directories. For more information visit the [restic documentation](https://restic.readthedocs.io/en/stable/).


## Role Variables

There are the required variables you need to set:

- `s3_backup_host`: Hostname of the S3 server
- `s3_backup_access_key`: Access key for the S3 Buckets
- `s3_backup_secret_key`: Secret key for the S3 bucket
- `s3_backup_bucket_URL`: URL of the S3 bucket to use
- `s3_backup_script`: Script to backup data
- `restic_repository_password`: Password of the restic repository

The retention is set to 7 days by default. This is in relation to the creation time of the latest snapshot.

- `retention_time`: Duration relative to the creation time of the latest snapshot after which snapshots in the repository expire (default 7d).
-  format: Use a combination of years (`y`), months (`m`), days (`d`), and hours (`h`). <br> <br> E.g. :`2y5m7d3h` for 2 years, 5 months, 7 days and 3 hours; `6m` for 6 months; `67h` for 67 hours
<br> If the latest snapshot was created on 20.08.26, then by default all snapshots created before 13.08.26 will expire.

Have a look at the [defaults](defaults/main.yml) to see all available variables
and how to use them.

## Prometheus Metrics

The backup automation can create metrics for Prometheus.
The metrics are written to file in S3 which is made public.
The metrics provide the time of the last successful backup.

```openmetrics
# HELP backup_time Time stamp of backup
# TYPE backup_time counter
backup_time{host="example.com"} 1735876976
```

## Example Playbook

Your playbook, could look like this:

```yaml
- hosts: all
  become: true
    - role: uos.s3_backup
      s3_backup_host: s3.example.com
      s3_backup_access_key: foo8thaochei8Pe2uu8alei9
      s3_backup_secret_key: axahle6OitieweiNgoh7gai2
      s3_backup_bucket_URL: s3:https://server:port/bucket_name
      restic_repository_password: password
      s3_backup_script: |
        mysqldump -u root --no-data dbname | gzip > "${DATE}.sql.gz"
        source /opt/backup/restic
        restic backup {DATE}.sql.gz
```

## License

[BSD-3-Clause](LICENSE)
