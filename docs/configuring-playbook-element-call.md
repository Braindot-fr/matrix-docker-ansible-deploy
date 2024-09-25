# Setting up Element Call (optional)

The playbook can install and configure [Element Call](https://github.com/vector-im/element-call) for you.

Element Call is a WebRTC-based video and voice calling platform that integrates with Matrix clients such as Element Web. It provides secure, decentralized communication with support for video calls, audio calls, and screen sharing.

See the project's [documentation](https://github.com/vector-im/element-call) to learn more.

## Decide on a domain and path

By default, Element Call is configured to be served on the Matrix domain (`call.DOMAIN`, controlled by the `matrix_element_call_hostname` variable).

This makes it easy to set it up, **without** having to adjust your DNS records manually.

If you'd like to run Element Call on another hostname or path, use the `matrix_element_call_hostname` and `matrix_element_call_path_prefix` variables.

## Adjusting DNS records

If you've changed the default hostname, **you may need to adjust your DNS** records accordingly to point to the correct server.

Ensure that the following DNS names have a public IP/FQDN:
- `call.DOMAIN`
- `sfu.DOMAIN`
- `sfu-jwt.DOMAIN`

## Adjusting the playbook configuration

Add the following configuration to your `inventory/host_vars/matrix.DOMAIN/vars.yml` file:

```yaml
matrix_element_call_enabled: true
# Set a secure key for LiveKit authentication
matrix_element_call_livekit_dev_key: 'your-secure-livekit-key'
```

## External databases

If your setup utilizes an external database, you may need to adjust the default configuration for Redis used by Element Call. Modify the defaults in group_vars/matrix_servers.yml or host_vars to suit your setup:

```yaml
matrix_element_call_redis_hostname: 'localhost'
matrix_element_call_redis_port: 6379
matrix_element_call_redis_password: ''
```

## Installing
After potentially adjusting DNS records and configuring the playbook, run the installation command again:
```yaml
ansible-playbook -i inventory setup.yml
```

## Usage
Once installed, Element Call integrates seamlessly with Matrix clients like Element Web. When the Element Call service is installed, the `/.well-known/matrix/client` file is also updated. A new `org.matrix.msc4143.rtc_foci` section is added to point to your JWT service URL (e.g., `https://sfu-jwt.DOMAIN`).

Additionally, the `/.well-known/element/element.json` file is created to help Element clients discover the Element Call URL (e.g., `https://call.DOMAIN`).

## Required Firewall and Port Forwarding Rules

To ensure the services function correctly, the following firewall rules and port forwarding settings are required:

LiveKit:

	•	Forward UDP ports 50100:50200 to the Docker instance running LiveKit.
	•	Forward TCP port 7881 to the Docker instance running LiveKit.

Element Call:

	•	Forward TCP port 443 to the server running Traefik (for Element Call).

Ensure these ports are open and forwarded appropriately to allow traffic to flow correctly between the services.

## Additional Information

Refer to the Element Call documentation for more details on configuring and using Element Call.