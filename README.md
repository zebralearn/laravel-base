# ZebraLearn Laravel Base Docker Image

[![Build and Publish](https://github.com/zebralearn/laravel-base/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/zebralearn/laravel-base/actions/workflows/docker-publish.yml)
[![Docker Security Scan](https://github.com/zebralearn/laravel-base/actions/workflows/docker-security.yml/badge.svg)](https://github.com/zebralearn/laravel-base/actions/workflows/docker-security.yml)
[![GitHub release](https://img.shields.io/github/v/release/zebralearn/laravel-base?include_prereleases&style=flat-square)](https://github.com/zebralearn/laravel-base/releases)
[![Container Registry](https://img.shields.io/badge/ghcr.io-zebralearn%2Flaravel--base-blue?logo=docker&style=flat-square)](https://github.com/zebralearn/laravel-base/pkgs/container/laravel-base)

Production-grade, highly-optimized Alpine Linux base image designed for modern Laravel and PHP applications. Pre-configured with PHP-FPM, RoadRunner, popular PECL extensions, and multi-architecture support (`linux/amd64` and `linux/arm64`).

---

## 🚀 Key Features

- **Multi-Architecture Support**: Native binaries for both `linux/amd64` (Intel/AMD) and `linux/arm64` (Apple Silicon, AWS Graviton).
- **Multi-PHP Matrix**: Supports **PHP 8.2**, **8.3**, **8.4**, and **8.5** (Default: 8.5).
- **High-Performance Runtimes**: Supports both standard **PHP-FPM** and **RoadRunner** (`rr`) out of the box.
- **Rich Extension Ecosystem**: Preloaded with high-throughput extensions including Swoole, Redis, MongoDB, RdKafka, gRPC, Protobuf, and Imagick.
- **Fast Build Times**: Uses precompiled Alpine packages to avoid slow and heavy compilation on ARM/QEMU builders.
- **Production Init**: Uses `tini` as the container entrypoint for proper signal forwarding and zombie process reaping.
- **Enterprise Security**: Automated Trivy vulnerability scanning, SARIF generation for the GitHub Security tab, and SBOM/provenance tracking on every release.

---

## 📦 Supported PHP Versions & Architectures

| PHP Version | Architectures | Base Image | Status |
|:---|:---|:---|:---|
| **8.5** *(Default)* | `amd64`, `arm64` | `php:8.5-cli-alpine` | Active |
| **8.4** | `amd64`, `arm64` | `php:8.4-cli-alpine` | Active |
| **8.3** | `amd64`, `arm64` | `php:8.3-cli-alpine` | Active |
| **8.2** | `amd64`, `arm64` | `php:8.2-cli-alpine` | Active |

---

## 🏷️ Image Tagging Convention

Images are published to the GitHub Container Registry (`ghcr.io`):

```bash
ghcr.io/zebralearn/laravel-base:<tag>
```

### Common Tags

- `latest` – Latest stable release on default PHP version (8.5)
- `8.5`, `8.5-fpm-alpine`, `php8.5` – Latest build for PHP 8.5
- `8.4`, `8.4-fpm-alpine`, `php8.4` – Latest build for PHP 8.4
- `8.3`, `8.3-fpm-alpine`, `php8.3` – Latest build for PHP 8.3
- `8.2`, `8.2-fpm-alpine`, `php8.2` – Latest build for PHP 8.2

### SemVer Release Tags

When a release version (e.g., `v1.1.0`) is published, the following tags are generated:

- Full version: `1.1.0`, `1.1.0-php8.5`, `1.1.0-php8.4`, `1.1.0-php8.3`, `1.1.0-php8.2`
- Minor version: `1.1`, `1.1-php8.5`, `1.1-php8.4`, etc.
- Major version: `1`, `1-php8.5`, `1-php8.4`, etc.

---

## 🛠️ Usage Examples

### 1. Using in a Laravel `Dockerfile` (RoadRunner or PHP-FPM)

```dockerfile
# Use your desired PHP version tag (e.g., 8.4 or 8.5)
FROM ghcr.io/zebralearn/laravel-base:8.4

# Set application working directory
WORKDIR /var/www/html

# Copy application files
COPY . .

# Set up permissions
RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache

# Example 1: Running with RoadRunner
CMD ["rr", "serve", "-c", ".rr.yaml"]

# Example 2: Running with PHP-FPM
# CMD ["php-fpm", "-F"]

# Example 3: Running a Laravel queue worker
# CMD ["php", "artisan", "queue:work", "--tries=3"]
```

### 2. Multi-Stage Dockerfile with Composer & Build Tools

```dockerfile
# Build Stage: Composer dependencies
FROM composer:2 AS vendor
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader

# Runtime Stage
FROM ghcr.io/zebralearn/laravel-base:8.4
WORKDIR /var/www/html

COPY --from=vendor /app/vendor ./vendor
COPY . .

CMD ["rr", "serve", "-c", ".rr.yaml"]
```

### 3. Docker Compose

```yaml
services:
  app:
    image: ghcr.io/zebralearn/laravel-base:8.4
    volumes:
      - .:/var/www/html
    ports:
      - "8000:8000"
    command: ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```

---

## 🧩 Included Packages & Extensions

### Core CLI & Runtime Tools
- `bash`, `ca-certificates`, `curl`, `tini`
- `nodejs`, `npm`
- `mysql-client`, `postgresql-client`
- **RoadRunner**: Binary located at `/usr/local/bin/rr`

### PHP Core & Extensions
- **Database & Storage**: `pdo`, `pdo_mysql`, `mysqli`, `pdo_pgsql`, `pgsql`, `redis`, `mongodb`
- **Concurrency & Messaging**: `swoole`, `pcntl`, `posix`, `sockets`, `rdkafka`, `grpc`, `protobuf`
- **Web & Media**: `curl`, `gd`, `imagick`, `exif`, `fileinfo`, `zip`
- **Text & Encoding**: `mbstring`, `iconv`, `intl`, `bcmath`, `sodium`
- **XML & Data**: `dom`, `simplexml`, `xml`, `xmlreader`, `xmlwriter`, `tokenizer`
- **Performance**: `opcache`, `phar`

---

## 💻 Local Development & Makefile

A feature-rich [`Makefile`](file:///Volumes/Data-2/Workspace/Zebralearn/laravel-base/Makefile) is provided to build and push images locally.

### Display Available Commands
```bash
make help
```

### List Tags That Will Be Generated
```bash
make list-tags
```

### Build Single PHP Version
```bash
# Build multi-arch image for PHP 8.4 locally
make build-8.4

# Or dynamically pass version
make build-php PHP_VER=8.4
```

### Build All PHP Versions
```bash
make build-all
```

### Build & Push (Requires Docker Registry Authentication)
```bash
# Push specific version
make push-8.4

# Push all versions and all semantic tags
make push-all
```

---

## 🔒 Security & Quality Assurance

This repository includes a production-grade automated vulnerability scanning workflow powered by **Aqua Security Trivy**:

- **Trigger**: Runs automatically whenever the **Build and Publish Docker image** release pipeline finishes (`on.workflow_run`), when a release is published directly (`on.release.types: [published]`), or on-demand via `workflow_dispatch`.
- **Coverage**: Scans every supported PHP version matrix (`8.2`, `8.3`, `8.4`, `8.5`).
- **SARIF Security Alerts**: Uploads detailed vulnerability reports to the repository's **GitHub Security Tab** (Code Scanning Alerts) with segregated categories per PHP version.
- **Workflow Step Summary**: Prints an immediate ASCII summary table in the GitHub Actions run summary for instant visibility into CVEs and patch availability.

---

## 📄 License & Maintainer

Maintained by **Kuldip Satpute** (<stputekuldip@gmail.com>) and the **ZebraLearn** engineering team.
