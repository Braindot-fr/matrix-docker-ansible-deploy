<!--
SPDX-FileCopyrightText: 2024 wjbeckett
SPDX-FileCopyrightText: 2024 - 2025 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Element Call (optional)

The playbook can install and configure [Element Call](https://github.com/element-hq/element-call) for you.

Element Call is a native Matrix video conferencing application developed by [Element](https://element.io), designed for secure, scalable, privacy-respecting, and decentralized video and voice calls over the Matrix protocol. Built on MatrixRTC ([MSC4143](https://github.com/matrix-org/matrix-spec-proposals/pull/4143)), it utilizes [MSC4195](https://github.com/hughns/matrix-spec-proposals/blob/hughns/matrixrtc-livekit/proposals/4195-matrixrtc-livekit.md) with [LiveKit](configuring-playbook-livekit-server.md) as its backend.

See the project's [documentation](https://github.com/element-hq/element-call) to learn more.

## Decide on a domain and path

By default, Element Call is configured to be served on the `call.element.DOMAIN` domain, controlled by the `matrix_element_call_hostname` variable.

This makes it easy to set it up, **without** having to adjust your DNS records manually.

If you'd like to run Element Call on another hostname or path, use the `matrix_element_call_hostname` and `matrix_element_call_path_prefix` variables.

## Adjusting DNS records

If you've changed the default hostname, **you may need to adjust your DNS** records accordingly to point to the correct server.

Ensure that the following DNS names have a public IP/FQDN:
- `call.element.example.com`
- `livekit.example.com`
- `sfu-jwt.example.com`

## Adjusting the playbook configuration

Add the following configuration to your `inventory/host_vars/matrix.DOMAIN/vars.yml` file:

```yaml
matrix_element_call_enabled: true
```

💡 Enabling Element Call will automatically enable the [LiveKit JWT Service](configuring-playbook-livekit-jwt-service.md) and [Livekit Server](configuring-playbook-livekit-server.md) services.

## Installing

After configuring the playbook and potentially [adjusting your DNS records](#adjusting-dns-records), run the [installation](installing.md) command: `just install-all` or `just setup-all`

## Usage

Once installed, Element Call integrates seamlessly with Matrix clients like [Element Web](configuring-playbook-client-element-web.md). When the Element Call service is installed, the `/.well-known/matrix/client` file is also updated. A new `org.matrix.msc4143.rtc_foci` section is added to point to your LiveKit JWT service URL (e.g., `https://matrix.example.com/lk-jwt-service`).

Additionally, the `/.well-known/element/element.json` file is created to help Element clients discover the Element Call URL (e.g., `https://call.example.com`).

## Required Firewall and Port Forwarding Rules

To ensure the services function correctly, the following firewall rules and port forwarding settings are required:

LiveKit:

- Forward UDP ports 50100:50120 to the Docker instance running LiveKit.
- Forward TCP port 7881 to the Docker instance running LiveKit.

Element Call:

- Forward TCP port 443 to the server running Traefik (for Element Call).

Ensure these ports are open and forwarded appropriately to allow traffic to flow correctly between the services.

## Additional Information

Refer to the Element Call documentation for more details on configuring and using Element Call.
