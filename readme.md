# Docker Full Stack Project with Backup and CI/CD Pipeline 🚀

imagine that you have a web project and you need a complete `full-stack Docker setup`, `CI/CD pipeline` and a `backup system` that sends backups via a platform to you, these are the things that no matter what technology you use or even what the project is, i made this open source template so you can use for all of your web projects.


## Project Structure 📁
```
├── .github
│   └── workflows
│       └── deploy.yaml           # CI/CD Pipeline (GitHub Actions)
├── backend
│   └── Dockerfile                # Backend Dockerfile (empty, customizable)
├── frontend
│   └── Dockerfile                # Frontend Dockerfile (empty, customizable)
├── docker-compose.yml            # Docker Compose file for multi-container setup
├── .env                          # Environment variables for configuration
└── backup
    ├── backup.sh                 # Backup script
    ├── crontab                   # Cron job configuration for scheduled backups
    └── Dockerfile                # Dockerfile for backup container
```

## Features ✨

- **PostgreSQL Database**: A `db` service running a PostgreSQL container.
- **Automated Backup**: A automated `db-backup` service that runs daily by defult [Customizing the Backup Frequency](#custom-id) backups of your PostgreSQL database and sends them via Telegram.
- **CI/CD Pipeline**: A simple GitHub Actions CI/CD pipeline that automates the deployment of your application.
- **Frontend & Backend Setup**: Empty Dockerfiles in `frontend/` and `backend/` for you to fill in your application code and customize as needed.


## Comming Soon Features ✨ 




## Quick Start 🚀

To get started with the project, clone the repository and set up your Docker environment:

```bash
git clone https://github.com/Greenstorm911/dockerized-full-stack-with-backup-and-ci-cd.git
cd dockerized-full-stack-with-backup-and-ci-cd
```

### Set up your environment variables 🌍

1. Create a `.env` file in the root directory of the project and define the following environment variables:

```env
# PostgreSQL settings
POSTGRES_DB=your_database_name
POSTGRES_USER=your_database_user
POSTGRES_PASSWORD=your_database_password

# Telegram bot settings
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

2. Customize the `frontend/` and `backend/` Dockerfiles by adding your application code and dependencies as needed.

### Run the application using Docker Compose 🐋

Run the application with Docker Compose:

```bash
docker compose up --build
```

This will build all the services (backend, frontend, PostgreSQL, and backup) and start them in their respective containers.

## Customizing the Backup Frequency ⏰ {#custom-id}

The backup container runs a cron job that automatically creates backups of your PostgreSQL database and sends them to your Telegram bot. The backup is configured to run every day at 3:00 AM (server time). You can customize the backup time by editing the `crontab` file in the `backup/` folder.

### Steps to change the backup time:

1. Open the `backup/crontab` file.
2. Modify the cron expression to change the time and frequency of backups. The format for cron expressions is:

```
* * * * * /path/to/command
- - - - -
| | | | |
| | | | +---- Day of the week (0 - 7) (Sunday = 0 or 7)
| | | +------ Month (1 - 12)
| | +-------- Day of the month (1 - 31)
| +---------- Hour (0 - 23)
+------------ Minute (0 - 59)
```

For example:
- `0 3 * * *` will run the backup at 3:00 AM every day.
- `0 0 * * 0` will run the backup every Sunday at midnight.

### Example of a cron job to run every hour:

```bash
0 * * * * /app/backup.sh >> /var/log/cron/cron.log 2>&1
```

3. Once you've customized the cron job, the container will run the backup script according to the new schedule.

### What happens during backup?

- The `backup.sh` script performs a PostgreSQL dump (`pg_dump`) of your database and creates a `.zip` file.
- The backup file is then sent to your Telegram bot via the Telegram Bot API.
- Old backups are automatically cleaned up, keeping only the last three backups.

## CI/CD Pipeline 🔄

The project includes a GitHub Actions CI/CD pipeline located in `.github/workflows/deploy.yaml`. It automates the deployment process.

You can modify this file to include your own deployment steps (such as building and pushing Docker images, deploying to a cloud provider, etc.).

## Backup Dockerfile 🗂️

The backup container uses an Alpine Linux-based image with PostgreSQL client and cron to perform backups. The Dockerfile (`backup/Dockerfile`) is as follows:

```Dockerfile
FROM alpine:3.19

RUN apk add --no-cache postgresql-client curl zip tzdata
RUN ln -sf /usr/share/zoneinfo/UTC /etc/localtime
RUN mkdir -p /var/log/cron /backups

WORKDIR /app
COPY backup.sh .
COPY crontab /etc/crontabs/root

RUN chmod +x backup.sh && \
    chmod 0644 /etc/crontabs/root && \
    touch /var/log/cron/cron.log

CMD ["busybox", "crond", "-f", "-L", "/var/log/cron/cron.log"]
```

This container is responsible for:

- Installing necessary dependencies (`postgresql-client`, `curl`, `zip`, and `tzdata`).
- Running the cron service that schedules and runs the `backup.sh` script.
- Storing backups in the `/backups` directory.

## Notes 📝

- Ensure your Telegram bot is configured and has the correct permissions to send files to the specified chat ID.
- Customize the `backend` and `frontend` Dockerfiles to suit your application needs.
- The `docker-compose.yml` file includes all services (`frontend`, `backend`, `db`, `db-backup`), but you can uncomment and configure them as needed.

## License 📜

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

Enjoy building your project! If you have any questions or run into any issues, feel free to open an issue or contribute to the repository. 🚀
