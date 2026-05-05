# Odoo Docker

Docker compose file to run Odoo in Docker with PostgreSQL, live code reloading (watch), and test coverage capabilities.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Changing Odoo Version

Updating the image tag lets you to use the desired Odoo version. By default, the compose file is configured to use Odoo 18 (`image: odoo:18`). You can easily switch to other versions by modifying the `image` field for the `odoo_web` and `odoo_test` services inside `odoo18-docker-compose.yml`.

**Examples:**
- For Odoo 17: change to `image: odoo:17`
- For Odoo 16: change to `image: odoo:16`
- For Odoo 15: change to `image: odoo:15`

## Usage

This project uses a custom-named compose file (`odoo18-docker-compose.yml`). Therefore, you must specify the filename using the `-f` flag in all your `docker compose` commands.

### Starting the Environment

To start the database and Odoo web server in the background:

```bash
docker compose -f odoo18-docker-compose.yml up -d
```

Once running, Odoo will be accessible at [http://localhost:8069](http://localhost:8069).

### Viewing Logs

To view the live logs of the running containers:

```bash
docker compose -f odoo18-docker-compose.yml logs -f
```

### Live Reloading (Docker Watch)

The compose file is configured with Docker Watch functionality. If you are developing custom modules and want the container to automatically restart when you make changes to your code, run:

```bash
docker compose -f odoo18-docker-compose.yml watch
```

*Note: You must update the volume host paths in `odoo18-docker-compose.yml` (e.g., `/home/user-name/Documents/OCA/...`) to point to your actual local addon directories before starting the containers or using watch.*

### Stopping and Removing Containers

To stop the running containers without removing them:

```bash
docker compose -f odoo18-docker-compose.yml stop
```

To stop and remove the containers, networks, and volumes (use with caution, as `down -v` will delete database data unless volumes are external):

```bash
docker compose -f odoo18-docker-compose.yml down
```

## Running Tests

The compose file includes a dedicated `odoo_test` service configured to run Odoo unit tests with Python `coverage`. This service is assigned to the `test` profile so it doesn't run during normal operation.

### Run Tests for Default Module

By default, it is configured to test the `account_move` module.

```bash
docker compose -f odoo18-docker-compose.yml --profile test run odoo_test
```

### Run Tests for Specific Modules

You can override the `MODULES_TO_TEST` environment variable to run tests for different modules:

```bash
docker compose -f odoo18-docker-compose.yml --profile test run --env MODULES_TO_TEST="module1,module2" odoo_test
```

### Run Tests with Specific Tags

You can specify particular test classes or methods using the `TEST_TAGS` environment variable:

```bash
docker compose -f odoo18-docker-compose.yml --profile test run --env MODULES_TO_TEST="account_move" --env TEST_TAGS="TestAccountMove" odoo_test
```

*(Examples of `TEST_TAGS`: `ClassName` or `ClassName.methodName`)*
