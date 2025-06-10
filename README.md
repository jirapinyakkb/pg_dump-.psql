# PostgreSQL Backup & Restore Test on Docker (Ubuntu 24.04)

This repository documents a step-by-step experiment on how to **install PostgreSQL**, create a database, **backup**, and **restore** it using `pg_dump` and `psql`.

## Environment

- Docker container: `ubuntu:24.04`
- PostgreSQL version: 12
- Installed manually inside the container

---


### 1. Start a Docker container

```bash
docker run -it --name backup_resotre ubuntu:24.04 /bin/bash
```

### 2. Update and install dependencies

```bash
apt update
apt -y upgrade
apt -y install vim nano bash-completion wget lsb-release
```
### 3. Add PostgreSQL APT repo and install PostgreSQL 12

```bash
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list
apt update
apt -y install postgresql-12 postgresql-client-12
```

## Setup Password Authentication

```bash
cat > /root/.pgpass <<EOF
localhost:5432:*:postgres:mysecretpassword
EOF

chmod 600 /root/.pgpass
export PGPASSFILE=/root/.pgpass
```

## Set the password for the postgres user:

```bash
su - postgres -c "psql -c \"ALTER USER postgres PASSWORD 'mysecretpassword';\""
```

## Create a Test Database & Table

```bash
psql -U postgres -h localhost -d postgres -c "CREATE DATABASE my_test_db;"
psql -U postgres -h localhost -d my_test_db
```

- Then, inside psql, create a table and insert data:

```bash
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name TEXT,
  position TEXT,
  salary INT
);

INSERT INTO employees (name, position, salary)
VALUES 
('Alice', 'Engineer', 60000),
('Bob', 'Manager', 75000),
('Charlie', 'Intern', 30000);
```

## Backup the Database

```bash
PGPASSFILE=/root/.pgpass pg_dump -U postgres -h localhost -d my_test_db -f /root/test_connection.backup -v -c
```

## Restore the Backup

- Create a new empty database first:

```bash
psql -U postgres -h localhost -d postgres -c "CREATE DATABASE restore_my_test_db;"
```

- Then restore the backup:

```bash
PGPASSFILE=/root/.pgpass psql -U postgres -h localhost -d restore_my_test_db -f /root/test_connection.backup
```

- Check that the data is restored:

```bash
psql -U postgres -h localhost -d restore_my_test_db -c "SELECT * FROM employees;"
```

### Additional Notes
- You can run backup automatically by creating a shell script, e.g. /root/pg_backup_test.sh
- PostgreSQL service must be started inside the container:

```bash
service postgresql start
```
- .pgpass helps avoid having to type your password every time

### What I Learned
- PostgreSQL must be installed inside the container before using psql or pg_dump
- psql is required to run SQL, while the shell (postgres@...$) is for system-level tasks
- Backup uses pg_dump, and restore can be done with psql -f or pg_restore (if using .backup from pg_dump in custom format)

### Clean Up
Stop or remove the container when done:

```bash
docker stop backup_resotre
docker rm backup_resotre
```
