# PostgreSQL Backup & Restore Test on Docker (Ubuntu 24.04)

This repository documents a step-by-step experiment on how to **install PostgreSQL**, create a database, **backup**, and **restore** it using `pg_dump` and `psql`.

## 🐳 Environment

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
