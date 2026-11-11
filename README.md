# Eastern Sierra Trip Planner

Natural-language fishing and camping trip planner for the Eastern Sierra, built on a PostGIS database of reconciled public land and water data.

**Status:** In early development. See [docs/scope.md](docs/scope.md) for scope and design decisions.

## Local setup

Requires Docker.

```bash
docker run --name sierra-postgis \
  -e POSTGRES_PASSWORD=<choose-a-password> \
  -p 5432:5432 -d postgis/postgis:16-3.4
```

Then create the database and enable PostGIS:

```bash
docker exec -it sierra-postgis psql -U postgres
```

```sql
CREATE DATABASE sierra;
\c sierra
CREATE EXTENSION IF NOT EXISTS postgis;
SELECT PostGIS_Version();
```

Note: data lives inside the container and is lost if the container is removed. Persistent storage is tracked in the issues.