---
name: containerfile-best-practices
description: Write or fix a Containerfile (or Dockerfile) so the image is secure and runs on Podman and Kubernetes. Use when creating, reviewing or repairing a Containerfile, Dockerfile or .containerignore.
license: Apache-2.0
compatibility: Podman 5.0+
metadata:
  version: "0.2.0"
---

# Containerfile best practices

Apply every rule below whenever you write or change a container build file. Then build and run the
image with Podman to check it works.

## Naming

- Name the build file `Containerfile`. When you repair an existing `Dockerfile`, rename it to
  `Containerfile` (`git mv` if the project uses git): Podman and Buildah read `Containerfile` first.
- Add a `.containerignore` next to it (not `.dockerignore`) excluding at least secrets (`.env`,
  `*.pem`, etc.), VCS data (`.git`, etc.) and local dependencies (`node_modules`, `.venv`,
  `__pycache__`, etc.).

## Base images

- Use **fully qualified** names: `quay.io/hummingbird/nodejs:26`, not `nodejs:26`. Short names
  depend on each host's registry search list and can resolve to another image.
- Pin every base image by **tag and digest**: `quay.io/hummingbird/nodejs:26@sha256:<digest>`.
  Get the digest with `skopeo inspect --format '{{.Digest}}' docker://<image>:<tag>` or
  `podman pull <image>:<tag> && podman image inspect --format '{{.Digest}}' <image>:<tag>`.
  Keep the tag for readers and update bots; the digest is what gets built.
- Prefer small, hardened bases such as the Hummingbird images (`quay.io/hummingbird/nodejs`,
  `quay.io/hummingbird/python`, `quay.io/hummingbird/go`, `quay.io/hummingbird/nginx`, ...), and use
  a multi-stage build when the app compiles or installs dependencies: build in the `-builder` tag
  (e.g. `quay.io/hummingbird/nodejs:26-builder`), run in the plain tag.

## Runtime

- Make the app health-checkable, e.g. a `/health` endpoint, so the orchestrator (Kubernetes probes,
  Quadlet `HealthCmd=`) can probe it.
- Run as a non-root user with a **numeric** UID. If the base image already sets one (Hummingbird
  images run as `65532`), keep it; otherwise set one, e.g. `USER 1001`, not `USER node`. Kubernetes
  `runAsNonRoot` can only verify a number.
- Use the exec (JSON) form for `CMD` and `ENTRYPOINT` so signals reach the app.
- Never copy secrets into the image: copy only the files the app needs, and keep `.env` out through
  `.containerignore`.

## Check your work

```sh
podman build -t localhost/app:dev .
podman run -d --rm --name app -p 8080:8080 localhost/app:dev
curl -fsS http://127.0.0.1:8080/health
```
