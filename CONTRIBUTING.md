# Contributing

Contributions are welcome. Please adhere to the following guidelines:

- Target stack is macOS, Docker Desktop, kind
- Document which parts are specific to that stack, and thus another setup may
  need to modify
- Keep it reasonably portable to Linux and other Docker and Kubernetes engines
- Keep bundled components declarative, no install/update scripts
- A full installation should require only `docker compose up -d` and
  `helmfile apply`
- Prefer vanilla, standard, widely accepted solutions when available
