# Pinnacle-HQ

## Project Summary

Pinnacle-HQ is the cloud-side fleet-management application for Pinnacle devices.

The repository is split into:

- An API service and agent gateway.
- A web application.
- Migrations, protocol definitions, and infrastructure.

`api/pyproject.toml` and `agent-gateway/pyproject.toml` identify Python service boundaries, while `web/package.json` and its TypeScript configurations define the browser application.

The API domain covers device and fleet data, pairing and authorization, telemetry, faults and alarms, logs, OTA updates, backup and restore, provisioning, factory reset, disable operations, and location data. The agent gateway provides the server-side path for device-facing operations.

PostgreSQL migrations cover devices, runs, results, logs, OTA, audit records, notifications, alerts, application settings, and result fields.

`infra/docker-compose.yml` and the Keycloak realm configuration describe local service composition and identity integration. The `proto` directory provides shared protocol assets.

The web package includes application and Vitest TypeScript configuration, indicating a browser test path. Makefile and validation templates provide repository automation.

The old README called this repository planning, but the current tree contains meaningful implementation and deployment artifacts.

It is not appropriate to claim production completeness: source inspection does not demonstrate an end-to-end device session, live migrations, Keycloak flows, or all web routes. The statuses below distinguish code/schema presence from verified behavior.

Run API, gateway, web, and migration checks from their manifests and Makefile. Then test authorization, streaming, OTA, and destructive fleet actions in an isolated environment.

## Feature Status

| # | Name / Description | Implemented? | Test Coverage? | Manual Test |
|---:|---|---|---|---|
| 1 | Python fleet API service | Yes | Service test configuration exists; endpoint coverage is not established | [ ] Manual verification required |
| 2 | Device pairing and authorization | Partial | Keycloak realm and service code exist; identity flow needs integration validation | [ ] Manual verification required |
| 3 | Agent gateway for device-facing operations | Yes | Gateway package exists; live device connectivity is unverified | [ ] Manual verification required |
| 4 | Fleet telemetry, faults, alarms, logs, and runs | Partial | Database/API surfaces exist; end-to-end event flow is unproven | [ ] Manual verification required |
| 5 | OTA, provisioning, backup/restore, and factory operations | Partial | Migrations and service boundaries exist; live operations need isolation | [ ] Manual verification required |
| 6 | Web fleet console | Partial | TypeScript/Vitest configuration exists; complete browser coverage is unknown | [ ] Manual verification required |
| 7 | PostgreSQL migrations and audit data | Yes | Ten migration files provide evolution; live migration evidence is absent | [ ] Manual verification required |

## Tests and Commands

Use the repository Makefile and the `pyproject.toml` and web package scripts for service, migration, and frontend checks. Docker Compose provides local dependencies.

## Limitations

- Live database, Keycloak, agent, OTA, and browser integration are environment-dependent.
- This is an implemented but integration-dependent fleet platform.
