# Production Capacity

## When to use this guide

Read this guide before choosing CPU, memory, and scheduling priority for a production Keycloak deployment.

## Current chart model

The chart supports two ways to size the main Keycloak container:

- explicit `resources`
- opinionated `resourcesPreset`

If `resources` is set, it always wins.

If `resources` is empty, the chart can apply one of these presets:

- `small`
- `medium`
- `large`

## Preset intent

`small`

- small production environments
- lower login concurrency
- simpler extension footprint

`medium`

- general production baseline
- moderate concurrency
- common reverse-proxy and monitoring setup

`large`

- heavier concurrency
- larger memory footprint
- more demanding production environments

These presets are not universal truth. They are starting points that reduce manual repetition in stricter platforms.

## Preset values

`small`

- requests: `500m` CPU, `1Gi` memory
- limits: `1` CPU, `2Gi` memory

`medium`

- requests: `1` CPU, `2Gi` memory
- limits: `2` CPU, `4Gi` memory

`large`

- requests: `2` CPU, `4Gi` memory
- limits: `4` CPU, `8Gi` memory

## Priority class

The chart already exposes `priorityClassName`.

Use it when:

- Keycloak is part of a platform control surface
- the cluster has eviction pressure
- the platform already defines workload priorities

Do not set a high-priority class casually. Priority must be aligned with the rest of the platform.

## Recommended baseline

For a serious production environment, a reasonable starting point is:

```yaml
resourcesPreset: medium
priorityClassName: platform-critical
```

If the platform does not define a suitable priority class, leave `priorityClassName` empty and rely on normal scheduling policy.

## Heavy startup profile

If the deployment uses a larger provider or theme footprint, combine capacity planning with:

```yaml
probes:
  profile: heavy-startup
```

This gives the pod more time to bootstrap before startup and readiness failures begin to matter.

## Final recommendation

- start with `resourcesPreset` only if the platform benefits from a consistent baseline
- move to explicit `resources` once real production telemetry is available
- keep `priorityClassName` aligned with cluster policy, not with guesswork
