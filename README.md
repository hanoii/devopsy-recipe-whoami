# devopsy-recipe-whoami

The smallest devopsy project: [traefik/whoami](https://github.com/traefik/whoami)
behind a [devopsy-traefik](https://github.com/hanoii/devopsy-traefik) server,
released with [devopsy](https://github.com/hanoii/devopsy-cli). Use it to try
targets, releases, domains and certificates, or as a starting point.

## Try it

Point the targets at your server in `.devopsy/.env` (gitignored), or in the
environment:

```sh
echo DEVOPSY_TARGET_HOST=devopsy@203.0.113.10 >> .devopsy/.env
```

Each environment also gets `<project>.<server's public domain>`, which
devopsy asks the server's Traefik for at each release. Override it, or set
it empty for none, per target: `devopsy @prod --vars set --show
DEVOPSY_PUBLIC_DOMAIN`.

```sh
devopsy @prod release          # runs deploy, prints https://whoami-prod.<server's public domain>
devopsy @prod domains          # DNS, challenge and certificate per host, and what next
devopsy @prod logs -f web
devopsy @prod releases
devopsy @staging release
```

`targets.yaml` defines `prod` and `staging` on the same server, each with its
own path, so its own containers and URL. `DEVOPSY_TARGET_HOST_STAGING` puts
staging on another server. Without a public domain or `DEVOPSY_DOMAINS`, a
released environment has no host at all: `release` says so.

## A custom domain

In `targets.yaml`, give the target its domains and release again:

```yaml
prod:
  env:
    DEVOPSY_DOMAINS: whoami.example.org
```

- **Default, HTTP-01:** point the domain at the server (an A record, or a
  CNAME to the public URL), then release. `devopsy @prod domains` shows when
  it is live; if the certificate came too early, `--retry`.
- **Before switching DNS, acme-dns:** add `CERTRESOLVER: acmedns`, release,
  and `devopsy @prod domains` prints the `_acme-challenge` CNAME to create.
  Once it exists, `devopsy @prod domains --retry` gets the certificate while
  the domain still points elsewhere; then switch DNS.

## Locally

Locally, without a public domain, the host is
`devopsy-recipe-whoami.localhost`, served by a local devopsy-traefik:

```sh
devopsy up -d
```

## License

MIT, so you can start your own project from it. See [LICENSE](LICENSE).
