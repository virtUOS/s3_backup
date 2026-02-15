# Ansible Role for Backups to S3 Buckets

This ansible script provides a simple way of confguring automatic backups to
an S3 bucket.

## Role Variables

There are the required variables you need to set:

- `s3_backup_host`: Hostname of the S3 server
- `s3_backup_access_key`: Access key for the S3 Buckets
- `s3_backup_secret_key`: Secret key for the S3 bucket
- `s3_backup_bucket`: Name of the S3 bucket to use
- `s3_backup_script`: Script to backup data

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
      s3_backup_bucket: backup
      s3_backup_script`: Script to backup data
        FILENAME="${DATE}.sql.gz"
        mysqldump -u root --no-data dbname | gzip > "${FILENAME}"
        s3_put "${FILENAME}" "${FILENAME}"
```

## License

[BSD-3-Clause](LICENSE)
