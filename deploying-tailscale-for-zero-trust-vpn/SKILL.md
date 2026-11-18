---
name: deploying-tailscale-for-zero-trust-vpn
description: Deploy and configure Tailscale as a zero-trust VPN — install on Linux servers, containers, and Kubernetes, write ACL/grant policies with tags, set up subnet routers and exit nodes, enable MagicDNS and Tailscale SSH, and harden with auth keys and key expiry. Use whenever the user mentions Tailscale, tailnet, WireGuard mesh, zero-trust network access, replacing a bastion/VPN, private access to servers or databases, or exposing internal services securely, even if they don't name Tailscale explicitly.
---

# Deploying Tailscale for Zero-Trust VPN

Tailscale builds a WireGuard mesh where each device authenticates with an identity provider and access is decided by a central policy file. "Zero trust" here means devices get no access by default. You grant access explicitly, by identity or tag, to specific ports. Keep that principle in every step: start from deny, then open only what's needed.

## 1. Gather context first

Before running anything, find out:
- **Targets.** Which machines need to join (VMs, containers, Kubernetes, laptops) and their OS.
- **Access goal.** Who should reach what, on which ports. This becomes the policy.
- **Admin access.** Does the user have tailnet admin access or an API key? Policy changes are made in the admin console or through the API.
- **Server vs person.** Servers should be **tagged** (owned by the tailnet, not a person) so access doesn't break when an employee leaves.

## 2. Install and join

Linux (any distro):
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --auth-key="$TS_AUTHKEY" --advertise-tags=tag:server --ssh
```
- Generate auth keys in the admin console (Settings → Keys). For servers, use **pre-approved, tagged, non-reusable** keys. Use **ephemeral** keys for short-lived nodes like CI runners and autoscaled instances.
- Never hardcode the key in scripts or commits. Read it from env vars or a secret manager.
- On servers, disable key expiry (admin console → machine → Disable key expiry), or the node drops off after 180 days.

Docker:
```yaml
services:
  tailscale:
    image: tailscale/tailscale:stable
    hostname: myapp
    environment:
      - TS_AUTHKEY=${TS_AUTHKEY}
      - TS_EXTRA_ARGS=--advertise-tags=tag:server
      - TS_STATE_DIR=/var/lib/tailscale
    volumes: [ "ts-state:/var/lib/tailscale" ]
    cap_add: [ NET_ADMIN ]
    devices: [ /dev/net/tun ]
  app:
    image: myapp
    network_mode: service:tailscale   # app is reachable only via the tailnet
volumes: { ts-state: {} }
```

Kubernetes: prefer the **Tailscale Kubernetes Operator** (Helm chart `tailscale/tailscale-operator`, with an OAuth client ID and secret). Expose a service by annotating it `tailscale.com/expose: "true"`, or use `loadBalancerClass: tailscale`.

## 3. Write the access policy (the zero-trust core)

Edit the tailnet policy file (HuJSON). Example:
```jsonc
{
  "tagOwners": {
    "tag:server": ["group:devops"],
    "tag:db":     ["group:devops"]
  },
  "groups": {
    "group:devops": ["alice@example.com"],
    "group:dev":    ["bob@example.com"]
  },
  "grants": [
    { "src": ["group:devops"], "dst": ["tag:server", "tag:db"], "ip": ["*"] },
    { "src": ["group:dev"],    "dst": ["tag:server"],           "ip": ["tcp:443", "tcp:80"] },
    { "src": ["tag:server"],   "dst": ["tag:db"],               "ip": ["tcp:5432"] }
  ],
  "ssh": [
    { "action": "check", "src": ["group:devops"], "dst": ["tag:server"], "users": ["root", "autogroup:nonroot"] }
  ],
  "tests": [
    { "src": "bob@example.com", "accept": ["tag:server:443"], "deny": ["tag:db:5432"] }
  ]
}
```
Guidelines:
- **Remove the default allow-all rule.** New tailnets ship with `*` → `*`. Leaving it in place defeats the zero-trust model.
- Grant to **groups and tags**, not individual IPs or users.
- Use the narrowest ports possible. Add `tests` so policy changes that would break or widen access fail validation.
- Use `"action": "check"` for SSH to production. It forces users to re-authenticate.

## 4. Optional building blocks

- **Subnet router** (reach a VPC or LAN without installing on every host):
  ```bash
  echo 'net.ipv4.ip_forward = 1' | sudo tee /etc/sysctl.d/99-tailscale.conf
  echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
  sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
  sudo tailscale up --advertise-routes=10.0.0.0/16 --advertise-tags=tag:router
  ```
  Then approve the route in the admin console, or with `autoApprovers` in the policy, and grant access to the CIDR in `grants`.
- **Exit node:** `tailscale up --advertise-exit-node`, then approve it. Clients use `tailscale set --exit-node=<name>`.
- **MagicDNS:** turn it on in DNS settings so services are reachable at `host.tailnet-name.ts.net`.
- **HTTPS / Serve:** `tailscale serve --bg 3000` exposes a local port to the tailnet with TLS. Use `tailscale funnel` only when it must be publicly reachable, and say so explicitly.

## 5. Harden

- Turn on **device approval** and **Tailnet Lock** for high-security tailnets.
- Close public SSH and DB ports in the cloud firewall or security groups once Tailscale access works. Keep a break-glass path, such as the cloud provider's console, until you've confirmed access.
- Manage the policy as code (GitOps via the `tailscale/gitops-acl-action` GitHub Action or the Terraform `tailscale` provider) so changes get reviewed.
- Use OAuth clients with minimal scopes for automation, not personal API keys.

## 6. Verify

```bash
tailscale status            # peers and their state
tailscale ping <host>       # direct vs DERP-relayed path
tailscale netcheck          # NAT / UDP diagnostics
tailscale debug prefs       # local config
```
Test both what **should work** and what **should be denied**. A zero-trust rollout isn't done until you've confirmed that the denials actually happen.

## Cautions

- `tailscale up` with new flags can reset previously set flags. Use `tailscale set` to change individual settings on a running node.
- Running `tailscale up` over an SSH session to a remote host can cut off your own access. Have console access ready.
- Ask before removing the allow-all rule, closing public ports, or changing a live policy. These changes affect everyone on the tailnet.
