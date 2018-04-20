# OrientDB Backup to S3

## Prerequisites

This playbook expects you to have the following items:
- OrientDB running in a docker container
- OrientDB must be running in distributed config as the container will be shut down temporarily
- Have awscli setup with `aws configure set <item> <value>`

## Running

I personally run this with a cronjob on my server. You can set the interval you prefer.

## Variables

There are a few variables defined under site.yml, change them to suit your needs.

## Concept

You could easily create an alternate version of this not running in docker.
The concept is that you shutdown a server which is included in a distributed cluster. Afterwards you run a small
backup script included under 'templates'. I use ansible facts to give the backup a unique name but you could also
use bash dates for example.
If you want to add something or add the a bash version of this make a PR!

## Backup Script

```
CONNECT plocal:/orientdb/databases/{{ database }} {{ admin_user }} '{{ admin_password }}'; freeze database; backup database /orientdb/bin/backup/{{ database }}-{{ ansible_date_time.iso8601 }}.zip; release database;

```

Important thing is that you do not add `exit;` to the end of this script, otherwise the exit code will be 1 which makes
ansible fail. Info in this related issue: https://github.com/orientechnologies/orientdb/issues/8228
