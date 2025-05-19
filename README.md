# Update-Major-and-minor-version-of-postgreSQL
In PostgreSQL, major version upgrades and minor version upgrades refer to different types of updates to the database software, and each has its own process and requirements:


---
---

# Major Version Upgrade

Definition: A major version upgrade involves upgrading to a new PostgreSQL release with new features, enhancements, and changes that may break compatibility with the 
```
previous version. For example:
From PostgreSQL 14 to PostgreSQL 15.
```
### Prerequisites
  - Ensure sufficient disk space for backup and temporary files.
  - Validate compatibility of extensions, plugins, and client applications.
  - Review release notes for deprecated or removed features.  
  - Take a full backup of the database.
  - Test the upgrade in a staging environment.

### Impact:
  - May involve catalog changes, which means the internal database structure may differ.
  - Application changes may be needed if the new version introduces incompatible behavior.
### Methods for Major Version Upgrade
```
--- Logical Backup and Restore ---
    - Take a full backup using tools like pg_dump or pg_dumpall.
    [ pg_dump -h hostname -p port -U username -Fp -d dbname -f /path/location/backup.sql ]
    [ pg_dumpall -h hostname -p port -U username -Fp -f /path/backupfile.sql ]

--- Install the new package from postgresql.org based on Os and pg version then ---
    [sudo dnf -qy module disable postgresql]
    [sudo dnf install -y postgresql16-server]
    [sudo dnf /usr/pgsql-16/bin/postgresql-16-setup initdb ]

--- create pg_upgrade dir for store up_upgrade utility temp files in "/var/lib/pgsql/pg_upgrade_dir" then give permissions for postgres user.
--- Stop old postgresql server ---
  [systemctl stop postgresql16]

--- Upgrade version byusing pg_upgrade (faster method) & (Preserves database files while upgrading system catalogs) ---
  [sudo -U postgres /usr/pgsql-17/bin/pg_upgrade -d /var/lib/pgsql/16/data/ -D /var/lib/pgsql/17/data/ -b /usr/pgsql-16/bin/ -B /usr/pgsql-17/bin/ --link]

--- Check new verison ---
  [sudo /usr/pgsql-17/bin/postgres --verison] or [select version();]

--- Start new verison of Postgresql ----

```
### Post-requisites
  - Verify application functionality with the new version.
  - Update any client drivers or applications if needed.
  - Remove old PostgreSQL binaries and configuration files after successful migration.
  - Monitor performance for any unexpected issues.
---
---

## Minor Version Upgrade

Definition: A minor version upgrade updates the database within the same major version to fix bugs, enhance security, and address performance issues. For example:
From PostgreSQL 15.2 to PostgreSQL 15.3.

### Prerequisites:
  - Take a backup of the database for safety.
  - Validate the installed extensions for compatibility.
  - Check release notes for any behavioral changes.

### Impact:
  - No changes to the internal database structure.
  - Typically does not require application changes.
  - Safe and faster compared to major upgrades.

### Method for Minor Version Upgrade

Binary Replacement:

Stop the database server.
```
systemctl stop postgresql-15
```

Update the postgresql
```
sudo dnf update -y postgresql*
```
start the database server.
```
systemctl start postgresql-15
```
This is typically seamless and does not require data dump/restore. and we restart the server after the update.

Post-requisites
Verify the server is running the upgraded version using SELECT version();.
Monitor the database logs for any errors or warnings.

---
---



How to Perform These Upgrades as a DBA

1. Major Version Upgrade Steps

Prepare:

Review compatibility in the PostgreSQL Release Notes.

Test the upgrade in a non-production environment.

Backup your data: pg_dumpall or pg_basebackup.


Upgrade:

Install the new version binaries.

Use pg_upgrade:

pg_upgrade -b /old/bin/path -B /new/bin/path -d /old/data/path -D /new/data/path

Restart the PostgreSQL server.


Validate:

Check the upgraded cluster with pg_upgrade’s --check option.

Test database and application behavior.



### Minor Version Upgrade Steps

Prepare:

Take a backup of the database.

Read the release notes for known issues.


Upgrade:

stop the PostgreSQL service:

sudo systemctl stop postgresql-16

Install the new version using the package manager.

sudo yum update postgresql16

start the PostgreSQL service:

sudo systemctl start postgresql-16


Validate:

Verify the version with:

SELECT version();

Check for errors in the PostgreSQL logs.

---
Best Practices

1. Plan Downtime: Schedule upgrades during maintenance windows.


2. Backup First: Always have a recent backup in case of failures.


3. Test: Perform upgrades in a test/staging environment before production.


4. Monitor: Post-upgrade, monitor the system for issues such as performance degradation or unexpected behavior.



Let me know if you need detailed examples of commands or configurations!
