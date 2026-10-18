# doodle-backend-challenge

Spring Boot service backed by PostgreSQL. On startup, Flyway applies the migrations in
`src/main/resources/db/migration` and Hibernate validates the schema; it never changes it.

## Run with Docker Compose

The only prerequisite is Docker with Docker Compose v2. Java and Maven are not needed: the application is
built inside Docker.

Build and start PostgreSQL and the application:

```bash
docker compose up --build
```

The application listens on http://localhost:8080 once the logs show `Started Application`. It starts only
after PostgreSQL is healthy.

Follow the application logs (in another terminal, or after starting with `up -d --build`):

```bash
docker compose logs -f app
```

Stop everything. Database data is kept in the `postgres-data` volume and is still there the next time you
start:

```bash
docker compose down
```

Stop everything and **delete the database data**:

```bash
docker compose down -v
```

## Configuration

No configuration file is needed. To change a setting, copy the example file and edit it:

```bash
cp .env.example .env
```

`.env` is git-ignored. Docker Compose reads it automatically, and so does the application when it runs
outside Docker from the project root. A variable set in your shell takes precedence over `.env`.

| Variable      | Default     | Description                                                   |
|---------------|-------------|---------------------------------------------------------------|
| `APP_PORT`    | `8080`      | Host port of the application (Compose only)                   |
| `DB_PORT`     | `5432`      | Host port of PostgreSQL, for local access                     |
| `DB_HOST`     | `localhost` | PostgreSQL host for the application running outside Docker    |
| `DB_NAME`     | `doodle`    | Database name                                                 |
| `DB_USERNAME` | `doodle`    | Database user                                                 |
| `DB_PASSWORD` | `doodle`    | Database password                                             |

The default credentials are **for local development only**. Never reuse them elsewhere.

Inside Compose, the application always connects to `postgres:5432`, so `DB_HOST` and `DB_PORT` never
affect it.

If a host port is already in use, set another one in `.env` (for example `DB_PORT=5433` or
`APP_PORT=8081`) and start again.

## Run the application locally (optional)

This needs Java 25 and Maven 3.9+. Start only the database, then run the application from the project root
so it can read `.env`:

```bash
docker compose up -d --wait postgres
mvn spring-boot:run
```

The application's own HTTP port is Spring's `SERVER_PORT` (default `8080`). It is not read from `.env`, so
set it in the shell if needed:

```bash
SERVER_PORT=8081 mvn spring-boot:run
```

Build and test without Docker:

```bash
mvn verify
```
