[![Docker Pulls](https://img.shields.io/docker/pulls/adaliszk/valheim-server?label=Docker%20Pulls)](https://hub.docker.com/r/adaliszk/valheim-server)
[![:latest image size](https://img.shields.io/docker/image-size/adaliszk/valheim-server/latest?label=Image%20Size)](https://hub.docker.com/r/adaliszk/valheim-server)
[![server build](https://github.com/adaliszk/valheim-server/actions/workflows/cd-server.yml/badge.svg?label=Server)](https://github.com/adaliszk/valheim-server/actions/workflows/cd-server.yml)
[![monitoring build](https://github.com/adaliszk/valheim-server/actions/workflows/cd-monitoring.yml/badge.svg?label=Monitoring)](https://github.com/adaliszk/valheim-server/actions/workflows/cd-monitoring.yml)
[![helm build](https://github.com/adaliszk/valheim-server/actions/workflows/cd-helm.yml/badge.svg)](https://github.com/adaliszk/valheim-server/actions/workflows/cd-helm.yml)
[![license](https://img.shields.io/github/license/adaliszk/valheim-server?label=License)](https://github.com/adaliszk/valheim-server/LICENSE.md)

# Valheim Docker Server & Helm Chart
Clean, fast and standalone Docker & Kubernetes helm deployments.

While there are many other images out there, they tend to fall into the bad habit of using anti-patterns, like using
Supervisor and Cron in a single image. The images included here aim to avoid these bad habits, while still offering a
full feature-set for managing and monitoring your Valheim Server.


## What features do the images have?
- A fully working Valheim Server **without the need of steam downloading** anything from the internet.
- Using a **non-root user** to mitigate potential vulnerabilities.
- **Gracefully stops the server**; enables proper saving before shutdown to avoid world corruption.
- **Automatic Backup** of the world files when the server saves them onto the disk.
- **Sanitized server output**; say goodbye to the debug noise that is not important!
- Health-checks to monitor the image's basic status
- Companion image for monitoring: [adaliszk/valheim-server-monitoring](https://hub.docker.com/r/adaliszk/valheim-server-monitoring)
<!--
- Helm chart for Kubernetes: [https://charts.adaliszk.io](https://charts.adaliszk.io/chart/?name=valheim-server)
-->

## Server:
[`adaliszk/valheim-server`](https://hub.docker.com/r/adaliszk/valheim-server)

Alternatives:
[`ghcr.io/adaliszk/valheim-server`](https://ghcr.io/adaliszk/valheim-server),
[`quay.io/adaliszk/valheim-server`](https://quay.io/adaliszk/valheim-server)

- `vanilla` `latest` - always the latest stable build of the server
- `0.218.21`, `0.218` - the server version released on 27/08/2024
- `bepinex-5.4.2202` `bepinex-5.4.21` `bepinex-5.4` `bepinex` - latest server using [denkinson's BepInEx](https://valheim.thunderstore.io/package/denikson/BepInExPack_Valheim) mod loader
- `plus-0.9.9.11` `plus-0.9.9`  `plus-0.9` `plus` - the latest server using [Valheim Plus](https://github.com/valheimPlus/ValheimPlus) mod
- `develop` - build any actively testing branch

additionally, there are version prefixed tags from `bepinex`, and `plus` variants, so you could specify exactly which
server you want to use, like: `0.204.04-bepinex-5.4.1601`

```bash
docker run -p 2456-2457:2456-2457/udp adaliszk/valheim-server -name "My Server" -password="super!secret"
```

or

```yaml
version: "3.8"
services:

  valheim:
    image: adaliszk/valheim-server
    environment:
      SERVER_NAME: "My custom message in the server list"
      SERVER_PASSWORD: "super!secret"
    ports:
      - 2456:2456/udp
      - 2457:2457/udp
```

[More details about using this image](docs/vanilla/README.md)

## Kubernetes deployment:

```bash
helm repo add adaliszk https://charts.adaliszk.io
helm upgrade --install --create-namespace --wait my-valheim-server adaliszk/valheim-server
```

## Monitoring companion:
[`adaliszk/valheim-server-monitoring`](https://hub.docker.com/r/adaliszk/valheim-server-monitoring)

Alternatives:
[`ghcr.io/adaliszk/valheim-server-monitoring`](https://ghcr.io/adaliszk/valheim-server-monitoring),
[`quay.io/adaliszk/valheim-server-monitoring`](https://quay.io/adaliszk/valheim-server-monitoring)

- `metrics` - mtail metrics from the latest server version
- `prometheus` - a pre-configured prometheus for docker environments

```bash
docker run --name my_server -d -p 2456-2457:2456-2457/udp adaliszk/valheim-server
docker run -d --volumes-from my_server:ro -d -p 3903:3903 adaliszk/valheim-server-monitoring:metrics
```

or

```yaml
version: "3.8"
volumes:
  - logs: {}
services:

  valheim:
    image: adaliszk/valheim-server
    environment:
      SERVER_NAME: "My custom message in the server list"
      SERVER_PASSWORD: "super!secret"
    volumes:
      - logs:/logs
    ports:
      - 2456:2456/udp
      - 2457:2457/udp

  metrics:
    image: adaliszk/valheim-server-monitoring:metrics
    volumes:
      - logs:/logs:ro
    ports:
      - 3903:3903
```

## Examples
- [Basic Docker setup using Docker managed volumes](docs/basic-Docker-setup.md)
- [Basic Docker-Compose setup](docs/basic-Docker-Compose-setup.md)
- [Basic Docker-Compose setup (docker-compose.yml)](docs/examples/compose-simple.yml)
- [Basic Compose with Modded server (docker-compose.yml)](docs/examples/compose-modded.yml)
- [Export Metrics with MTail](docs/export-metrics-with-MTail.md)
- [Export Metrics with MTail (docker-compose.yml)](docs/examples/compose-with-metrics.yml)


## Contributions
Feel free to open Tickets or Pull-Requests, however, keep in mind that the idea is to keep it simple, and separate the
concerns into multiple small images that are ready without needing to download anything from the internet.

If you have questions, please use the [Discussions](https://github.com/adaliszk/valheim-server/discussions) tab or ping
me on the [Valheim Discord server](https://discord.gg/valheim): `Kicsivazz#2537`
