# admin-network

Shared network config, referenced by other repos as an ArgoCD multi-source
values file — not a deployed workload itself, same category as
`admin-github`/`admin-openbao`.

## `local-hosts.yaml`

The hostname → IP map for every standalone `.local` device on the LAN
(SteamOS boxes, standalone Pis, laptop). This is the single source of
truth for that list; it's referenced as an extra Helm values source by:

- `homelab-pihole` — turns each entry into an `address=/host/ip` static
  DNS record.
- `homelab-coredns` — turns each entry's hostname (IP unused there) into
  an explicit CoreDNS zone forwarding that name to Pi-hole.

Each of those repos' own `homelab-apps` Application entry pulls this file
in via `ref` + `valueFiles`, the same pattern used for `k8s-openbao`'s
chart/values split. Changing this file and pushing is enough — both
consuming Applications pick it up and resync independently, no manual
trigger needed.

If a device's IP ever changes (DHCP reservation updated, etc.), update it
here once, not in both consuming repos.

## `ingress-hosts.yaml`

The `*.morrisons.site` ingress hostnames and the single node IP
(`ingressAddress`, `imac`) they all resolve to. Only `homelab-pihole`
consumes this today — it's not duplicated anywhere else, this file exists
purely so every hardcoded network IP in the homelab lives in one obvious,
visible place rather than for dedup.
