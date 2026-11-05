# OrderFlow v2.3.0 Release Readiness Pack

## Release information

- **Version:** `v2.3.0`
- **Environment:** production
- **Artifact:** immutable Amazon ECR image tagged `v2.3.0`
- **Release candidate:** validated application and container build

## Deployment strategy

The GitHub Actions pipeline runs syntax checks and unit tests before building the
container image. A successful build is required before the `production` job can
start. Production uses an AWS EKS managed Kubernetes cluster with a three-replica
rolling update, zero unavailable replicas, and `/health` readiness and liveness
probes.

The production job is attached to the protected GitHub environment named
`production`. Required reviewers must approve that environment before AWS
credentials are used or the deployment runs.

## Rollback strategy

If health probes fail, error rates exceed the documented threshold, latency
degrades, or pods enter `CrashLoopBackOff`, run `scripts/rollback.sh v2.2.0`.
The script performs `kubectl rollout undo`, waits for the rollout, and verifies
the stable release health checks. No destructive database migrations are part of
this release.

## Production approval checklist

- [ ] Engineering manager approved the protected `production` environment.
- [ ] Operations on-call approved the protected `production` environment.
- [ ] AWS role is supplied through GitHub Actions OIDC; no long-lived key is stored.
- [ ] Kubernetes secret `lab-app-secrets` exists in the production namespace.
- [ ] Release image `v2.3.0` is present in the immutable ECR repository.
- [ ] Rollback target `v2.2.0` is available.

## Validation summary

- JavaScript syntax check: passed
- Unit tests: passed
- Container build: required before production deployment
- Kubernetes rolling update and health probes: configured
- Secret sanitization: passed; values are referenced from Kubernetes secrets
