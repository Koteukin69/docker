# Docker Services

Personal Docker Compose stacks on a single Debian 13 host. Each service is
self-contained in its own directory, so it can be moved, backed up, or
restored independently.

## Layout

```
docker/
├── .gitignore                 ← excludes .env and */data/
├── service/
│   ├── docker-compose.yml     ← service definition
│   ├── .env                   ← secrets
│   └── data/                  ← bind-mounted data
└── service2/
    └── ...
```

Conventions:

- **One directory per stack** — compose file, `.env`, and data live together.
- **`./data` paths** in compose files, so data is plain files on disk.
- **`.env` for secrets** — never committed.
- **`restart: unless-stopped`** for services that must survive reboots.

## Usage

Run from inside a stack's directory:

```bash
docker compose up -d        # create and start
docker compose logs -f      # follow logs
docker compose pull         # fetch new images
docker compose up -d        # recreate only what changed (update)
docker compose down         # stop (data stays)
docker compose down -v      # deletes data
```

## Backups

1. **Config** — this git repo. Commit every change and push to a remote.
2. **Databases** — dumps, not file copies (`pg_dump` / `mysqldump`).
3. **Data** — incremental and off-site (restic/borg). Stop the stack during
   the backup for a consistent snapshot.

Git + DB dumps + off-site copy = full restore of any stack on a clean machine.
