# Infrastructure Core Services

Docker compose stack used within my infrastructure. This stack runs on each node.

Clone this to your own repository and use it as a starting point to deploy some simple yet effective Docker powered
infrastructure that fills the shoes of Kubernetes in single-server/small environments.

See these blog posts for more information:

- https://blog.southcla.ws/2018/10/20/infrastructure-as-code-for-personal-projects/
- https://blog.southcla.ws/2018/11/08/a-modern-approach-to-single-server-infrastructure/

## Configuration

### Traefik

Set your email in `traefik.toml` for LetsEncrypt certs.

### Environment Variables

- `DATA_DIR`: The directory on the host where persistent data is stored.
- `TOP_LEVEL_DOMAIN_NAME`: Your TLD
- `MACHINE_NAME`: The name of the machine this stack runs on.
- `CORE_SERVICES_BASIC_AUTH`: An auth token for Traefik, cAdvisor and Prometheus

Why are domains required for this stack?

This stack exposes dashboards and control panels to the internet so a TLD is combined with a per-machine hostname to
build domains for these services.

## Deployment

### Development/private

Populate `.env` and `docker-compose up`.

`CORE_SERVICES_BASIC_AUTH` isn't _that_ important as the only thing you can do with it is view cAdvisor information,
query Prometheus and access Traefik's control panel. But it's still a password and should be treated as such. So, when
deploying to a real production service I highly recommend you use the following section.

### Production/public

It's highly advised you use Wadsworth to deploy this so you can:

- Get GitOps-style automatic restarts when configs change
- Handle loading secrets from [Vault](https://www.vaultproject.io/)

Config:

```js
T({
  name: "infra_core",
  url: "git@github.com:username/my-infrastructure",
  up: ["docker-compose", "up", "-d"],
  down: ["docker-compose", "down"]
});
```

This will automatically run `docker-compose up -d` every time your infra repo receives commits.
