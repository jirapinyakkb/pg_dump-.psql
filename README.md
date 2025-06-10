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
