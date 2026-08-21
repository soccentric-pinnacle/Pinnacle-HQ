# Pinnacle-HQ Build and Deployment

## Purpose and scope

Pinnacle-HQ is the local orchestration surface for the Python API, Python gRPC agent gateway, Vue/Vite web frontend, and Docker Compose services. The root Makefile provisions a Python 3.12 virtualenv, editable API/gateway installs, npm dependencies, Postgres, MinIO, Redis, and Keycloak, then runs migrations and background services.

## 1. Prerequisites

- Docker with the Compose plugin (`docker compose`) and a running daemon.
- Python 3.12 or newer, `python3 -m venv`, and Node.js/npm.
- Network access to pull Compose images and install Python/npm dependencies.
- Ports available: Postgres `5432`, MinIO `9000`/`9001`, Redis `6379`, Keycloak `8081`, API `8000`, web `5173`, and gateway `50051`.
- Enough local storage for persistent Docker volumes. The Compose file uses development credentials and must not be exposed beyond a trusted local network.

Check tools with:

```bash
make doctor
```

## 2. Setup

Create the venv and install editable API/gateway dev dependencies plus web dependencies:

```bash
make install
```

The API and gateway manifests require Python `>=3.12`; their dev extras include pytest and Ruff. The frontend uses `web/package.json` and its lockfile. Configure `DATABASE_URL`, `TEST_DATABASE_URL`, and `S3_ENDPOINT_URL` as needed when deviating from the Makefile defaults.

## 3. Build and compile

The root build target builds the Vite web frontend:

```bash
make build
```

The output is under `web/dist/`. Python services are interpreted packages and have no separate root compile target.

## 4. Test and lint

Run all three suites:

```bash
make test
```

Or run a slice:

```bash
make test-api
make test-gateway
make test-web
```

The API/gateway tests receive the configured test database and S3 endpoint; `make db-up` creates the test database. Quality checks:

```bash
make lint
make lint-api
make lint-gateway
make lint-web
```

`lint-web` runs npm lint and `vue-tsc --noEmit`; Python lint uses the venv Ruff executable.

## 5. Packaging and artifacts

There is no Python wheel, container image, or release packaging target in the root Makefile. Artifacts are the Vite `web/dist/` directory, Python source packages, migration files, and the running Compose service state. Any production packaging must be supplied by the deployment system and must preserve the API/gateway/frontend dependency contracts.

## 6. Deployment, install, and run

Start the development dependencies and initialize the test database/bucket:

```bash
make db-up
make migrate
make seed EMAIL=admin@example.com
```

`seed` requires a non-empty `EMAIL` and writes a development tenant/admin. Compose data persists in named volumes; the file documents dev passwords for Postgres, MinIO, and Keycloak, so treat this environment as local-only.

Start API, gateway, and web in the background:

```bash
make run
```

The Makefile prints LAN URLs and dev login details. It writes PIDs/logs under `.run/`. Tail logs or stop application processes:

```bash
make logs
make stop
make db-down
```

`stop` leaves the database containers running; `db-down` stops Compose services. A production deployment must provide real OIDC credentials, secrets, TLS, database/object-store configuration, service supervision, and ingress. None are configured by this Makefile.

## 7. Cleanup

```bash
make clean
```

This removes the venv, `.run`, `web/node_modules`, `web/dist`, caches, and Python egg-info directories. It does not remove Docker named volumes; use `make db-down` and an explicit Compose volume cleanup only when data loss is intended.

## 8. Hardware, cloud, and manual verification caveats

- HQ has no direct hardware build; device behavior depends on a reachable compatible agent/device and its gRPC contract.
- `make run` binds services to `0.0.0.0` and uses LAN-IP auth redirects; verify the chosen origin, cookies, OIDC realm, and firewall manually.
- The Compose Keycloak admin/dev credentials and application seed password are not production credentials.
- Validate database migrations, object-store persistence, auth linking, API/gateway connectivity, browser workflows, and device telemetry manually in a representative environment. Cloud deployment, managed databases, and secret management are external concerns.
