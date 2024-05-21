# PostgreSQL Docker Compose Deployment

This repository contains a Docker Compose configuration for deploying a PostgreSQL database on a production server. The setup includes environment variables for easy configuration and log rotation to manage log sizes.

## Prerequisites

- Docker > version 20
- Docker Compose > v2

## Setup

### 1. Clone the Repository

```sh
git clone https://github.com/your-username/your-repo.git
cd your-repo
```

### 2. Create the .env File

Create a `.env` file in the root directory of the repository and add the following content:

```env
COMPOSE_PROJECT_NAME=yourproject
POSTGRES_DB=mydatabase
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mypassword
POSTGRES_CONTAINER_NAME=postgres
POSTGRES_IMAGE=postgres:13
POSTGRES_PORT=5432
POSTGRES_LOG_MAX_SIZE=10m
POSTGRES_LOG_MAX_FILE=3
```

Replace the placeholder values (`mydatabase`, `myuser`, `mypassword`, etc.) with your actual configuration.

### 3. Update docker-compose.yml

The `docker-compose.yml` file is already configured to use the environment variables from the `.env` file. Ensure it looks like this:

```yaml
version: '3.8'

services:
  postgres:
    image: ${POSTGRES_IMAGE}
    container_name: ${POSTGRES_CONTAINER_NAME}
    restart: always
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "${POSTGRES_PORT}:5432"
    logging:
      driver: "json-file"
      options:
        max-size: ${POSTGRES_LOG_MAX_SIZE}
        max-file: ${POSTGRES_LOG_MAX_FILE}

volumes:
  postgres_data:
    driver: local
```

### 4. Start the PostgreSQL Service

Run the following command to start the PostgreSQL service:

```sh
docker-compose up -d
```

### 5. Verify the Setup

You can verify that the PostgreSQL container is running with the following command:

```sh
docker ps -a
```

You should see a container named `postgres` (or whatever you set in `POSTGRES_CONTAINER_NAME`) running.

### 6. Accessing PostgreSQL

You can connect to the PostgreSQL database using a PostgreSQL client or through a command-line tool like `psql`:

```sh
docker-compose exec postgres psql -h localhost -U myuser -d mydatabase
```

Replace `myuser` and `mydatabase` with your actual username and database name. You will be prompted for the password you set in the `.env` file.

## Log Management

Logs are configured to rotate with a maximum size of 10MB and a maximum of 3 log files retained. This prevents excessive disk usage by logs.

## Customization

You can customize the configuration further by modifying the `.env` file and the `docker-compose.yml` file to suit your needs.

## Troubleshooting

If you encounter any issues, you can check the logs of the PostgreSQL container:

```sh
docker-compose logs postgres
```

Replace `postgres` with the name of your container if different.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
