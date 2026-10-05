# devopsy-recipe-whoami

The smallest devopsy project: [traefik/whoami](https://github.com/traefik/whoami)
behind a [devopsy-traefik](https://github.com/hanoii/devopsy-traefik) server,
released with [devopsy](https://github.com/hanoii/devopsy-cli). Use it to try
targets, releases, domains and certificates, or as a starting point.

## Try it

```sh
devopsy @prod release deploy   # prints https://whoami-prod.<server's public domain>
devopsy @prod domains          # DNS, challenge and certificate per host, and what next
devopsy @prod logs -f web
devopsy @prod releases
devopsy @staging release deploy
```

`targets.yaml` defines `prod` and `staging` on the same server, each with its
own path, so its own containers and URL.

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

Without a server's public domain, the host is `whoami-recipe.localhost`, served
by a local devopsy-traefik:

```sh
devopsy up -d
```
