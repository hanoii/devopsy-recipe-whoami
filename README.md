# devopsy-template-whoami

The smallest devopsy project: [traefik/whoami](https://github.com/traefik/whoami)
behind a [devopsy-template-traefik](https://github.com/hanoii/devopsy-template-traefik) server,
released with [devopsy](https://github.com/hanoii/devopsy-cli). Use it to try
targets, releases, domains and certificates, or as a starting point.

## Using this template

A starting point to own, not a dependency: start a project from it on
GitHub ("Use this template"), or clone it and keep this repository as a
remote (`upstream`) to pull its changes when you choose. Nothing updates
your copy, or what runs on your servers, but your own release.

## Try it

Point the targets at your server in `.devopsy/.env` (gitignored), or in the
environment:

```sh
echo DEVOPSY_TARGET_HOST=devopsy@203.0.113.10 >> .devopsy/.env
```

Each environment also gets `<project>.<server's wildcard domain>`, which
each release imports from the server's proxy (devopsy-template-traefik), through the
`devopsy.import` label in `compose.yaml`: a release fails while no proxy
runs. The release says what it imported; `devopsy @prod --debug imports`
shows it later, and whether the proxy has changed it since. Override it,
or set it empty for none, per target: `devopsy @prod --vars set --show
DEVOPSY_WILDCARD_DOMAIN`.

```sh
devopsy @prod --release          # runs deploy, prints https://whoami-prod.<server's wildcard domain>
devopsy @<server>-traefik domains whoami-prod   # DNS, challenge and certificate per host, and what next
devopsy @prod logs -f web
devopsy @prod --releases
devopsy @staging --release
```

`targets.yaml` defines `prod` and `staging` on the same server, each with its
own path, so its own containers and URL. `DEVOPSY_TARGET_HOST_STAGING` puts
staging on another server. Without a wildcard domain or `DEVOPSY_DOMAINS`, a
released environment has no host at all: `--release` says so.

## A custom domain

In `targets.yaml`, give the target its domains and release again:

```yaml
prod:
  env:
    DEVOPSY_DOMAINS: whoami.example.org
```

- **Default, HTTP-01:** point the domain at the server (an A record, or a
  CNAME to the wildcard URL), then release. `devopsy @<server>-traefik
  domains whoami-prod` (devopsy-template-traefik) shows when it is live; if the
  certificate came too early, `--retry`. `devopsy --probe whoami.example.org`
  checks it from your machine.
- **Before switching DNS, acme-dns:** add `CERTRESOLVER: acmedns`, release,
  and `devopsy @<server>-traefik domains whoami-prod` prints the
  `_acme-challenge` CNAME to create. Once it exists, the same with `--retry`
  gets the certificate while the domain still points elsewhere; then switch
  DNS.

## Locally

Locally, without a wildcard domain, the host is
`devopsy-template-whoami.localhost`, served by a local devopsy-template-traefik:

```sh
devopsy up -d
```

## License

MIT, so you can start your own project from it. See [LICENSE](LICENSE).
