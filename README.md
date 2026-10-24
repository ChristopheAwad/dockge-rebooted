# Dockge Rebooted

A personal fork of [Dockge](https://github.com/louislam/dockge), a Docker Compose stack manager. The aim is to maintain a useful personal fork that can support community use as it matures.

## Status

Early bootstrap, version `2.0.0-alpha.0`. There are no new application capabilities yet. This fork has its own branding, container build, and repository workflows.

As of October 2026, upstream has had no tagged release since `1.5.0` in March 2025, with limited recent maintenance activity. This project develops independently and is not affiliated with Louis Lam or the upstream Dockge project.

The inherited automatic update checker is disabled. Check this repository for updates. The initial image supports `linux/amd64` only. Images are published on demand and on version tags, not on a nightly schedule.

Each image publication also updates the mutable `edge` tag. Use a version tag for deployment, not `edge`.

## Side-by-side installation

The image must be published and public before these instructions work. Check the image build result in [Actions](https://github.com/ChristopheAwad/dockge-rebooted/actions).

Back up your existing Dockge data and stacks first. On your Docker host:

```sh
sudo mkdir -p /opt/dockge-rebooted /opt/stacks-rebooted
cd /opt/dockge-rebooted
sudo curl -fL https://raw.githubusercontent.com/ChristopheAwad/dockge-rebooted/main/compose.rebooted.yaml -o compose.rebooted.yaml
sudo docker compose -f compose.rebooted.yaml config
sudo docker compose -f compose.rebooted.yaml up -d
sudo docker compose -f compose.rebooted.yaml ps
```

Open `http://YOUR_SERVER_IP:5002`. Create an admin account and log in. The supplied file uses its own Compose project, `./data-rebooted`, and `/opt/stacks-rebooted`. It does not share the official instance's database or stack files. The two sides of the stacks volume mount must use the same absolute path.

Before relying on the fork, confirm that its container is healthy, create a test stack, and check that its files are in `/opt/stacks-rebooted`. Confirm that your official instance still works. Stop this instance with `docker compose -f compose.rebooted.yaml down` if a check fails. Keep its data for inspection.

`compose.yaml` is the standard fresh-install example on port `5001`. Use `compose.rebooted.yaml` for side-by-side testing.

## Security

Access to the Docker socket gives control of the host. Run this application on a trusted host and keep it on your trusted LAN. Use a strong admin password. Do not commit environment files, credentials, or runtime data.

This fork inherits upstream dependencies and security risks. This bootstrap is not a security audit. Report vulnerabilities privately as described in [SECURITY.md](SECURITY.md).

## Development

Use Node.js 22.14 or later in the Node 22 line and npm.

```sh
npm ci
npm run lint
npm run check-ts
npm run build:frontend
```

`check-ts` checks the backend and shared code. The frontend build does not provide full Vue type-checking. No unit or end-to-end test harness is included yet.

With Docker available, `npm run build:docker` builds and loads an amd64 image tagged `ghcr.io/christopheawad/dockge-rebooted:dev`. It does not publish the image. The healthcheck compiler version is inherited from upstream and needs a future upgrade.

## License and attribution

MIT. Original Dockge code copyright Louis Lam. Fork changes copyright ChristopheAwad. See [LICENSE](LICENSE). Upstream history is preserved; upstream release tags are not published by this repository.
