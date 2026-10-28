# Contributing

Contributions are welcome. Please adhere to the following guidelines.

## Target stack

- The target stack is macOS, Docker Desktop, `kind`. Document which parts are
  specific to that stack, and thus what someone with a different setup may need
  to modify.

- Try to keep it reasonably portable to Linux and other Docker and Kubernetes
  engines. Windows support is not expected.

## Conventions

- Keep bundled components declarative, no scripts. A fresh install should only
  require `docker compose up -d` and `helmfile apply`.

- Prefer vanilla, widely accepted solutions when available. This repo is meant
  to be both teaching example as well as an easy to set up starting point. Keep
  complexity low.

- Always use the latest versions, do not pin versions, not even to a major
  version. This is tooling for local development, it may occasionally break
  after an update, that's accepted to avoid spreading potentially vulnerable
  packages. For this reason, set `pull_policy: always` on all Docker Compose
  services.
