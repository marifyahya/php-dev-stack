# PHP Dev Stack

A fast, lightweight, and centralized Docker environment designed specifically for **local development**. Whether you are building with Laravel, CodeIgniter, or modern PHP, this setup provides everything you need to run your apps locally.

Instead of running separate, heavy Docker containers for every single project (like Laravel Sail), this stack runs one single, shared set of services on your local machine. This saves your computer's resources (RAM/CPU) while allowing you to host and work on multiple PHP applications simultaneously. 

## Features
- **Multi-PHP Support:** Run projects on PHP 7.4, 8.3, or 8.4 simultaneously.
- **Optimized for Dev:** Includes Xdebug (Coverage mode), OPcache (Dev mode), Redis, and common PHP extensions.
- **Centralized Infrastructure:** Nginx, MySQL 8.0, and Redis shared across all projects.
- **Local Domains:** Easily map projects to custom `.test` domains.

---

## Directory Structure
To use this stack effectively, place this repository (`local-dev`) inside a parent "workspace" directory, alongside your actual code projects:

```text
working-space/
├── local-dev/                 <-- This repository
│   ├── docker-compose.yml
│   └── nginx/conf.d/
├── project-1/                 <-- Your PHP 8.3 project
└── project-2/                 <-- Your PHP 7.4 project
```

---

## Getting Started

1. **Setup Environment Variables:**
   ```bash
   cp .env.example .env
   ```
   *(Optional: Edit `.env` to change default ports or the MySQL root password).*

2. **Build and Start:**
   ```bash
   docker compose up -d --build
   ```

---

## Adding a New Project

To add a new project (e.g., `project-3`) to the stack:

1. **Create Nginx Config:**
   Copy the provided sample config:
   ```bash
   cp nginx/conf.d/example-app.conf.sample nginx/conf.d/project-3.conf
   ```
2. **Update Config:**
   Edit `project-3.conf` and update:
   - `server_name` (e.g., `project-3.test`)
   - `root` (e.g., `/var/www/html/project-3/public`)
   - `fastcgi_pass` (Choose `php74:9000`, `php83:9000`, or `php84:9000`)
3. **Restart Nginx:**
   ```bash
   docker compose restart nginx
   ```
4. **Update Hosts File:**
   Add your new domain to your Mac/PC's hosts file (`sudo nano /etc/hosts`):
   ```text
   127.0.0.1 project-3.test
   ```

---

## Running Commands (Artisan, Composer)

Do not run PHP commands directly on your host machine. Instead, run them inside the isolated Docker containers to guarantee you are using the correct PHP version and extensions.

**Enter the PHP 8.3 Container:**
```bash
docker compose exec php83 bash
cd project-1
php artisan migrate
```

**Enter the PHP 7.4 Container:**
```bash
docker compose exec php74 bash
cd project-2
composer install
```

---

## Connecting Services

When connecting your applications (like Laravel) to the database or cache, use the internal Docker network hostnames, not `localhost`.

### MySQL
- **Host:** `mysql`
- **Port:** `3306`
- **Username:** `root`
- **Password:** `root` (or whatever is in your `.env`)

### Redis
- **Host:** `redis`
- **Port:** `6379`
- **Password:** `null` (unless manually configured)

*(Note: If you are connecting from a GUI tool on your host machine like DBeaver or TablePlus, use `127.0.0.1` as the host instead).*
