# stephdewit.docker

Installs Docker CE on Debian and configures optional features: a minimum API version constraint and a TLS-secured TCP socket.

## Requirements

- Debian (jessie through trixie)
- Ansible 2.6+

## Role variables

### APT repository

| Variable | Default | Description |
| --- | --- | --- |
| `docker_apt_repository.arch` | `amd64` | CPU architecture for the APT repository |
| `docker_apt_repository.gpg_key_url` | Docker's official GPG key | URL of the signing key |
| `docker_apt_repository.url` | Docker's official repository | APT repository base URL |
| `docker_apt_repository.distribution` | `trixie` | Debian release codename |
| `docker_apt_repository.components` | `stable` | APT components |

### Paths

| Variable | Default | Description |
| --- | --- | --- |
| `docker_configuration_directory` | `/etc/docker` | Docker configuration directory |
| `docker_ssl_directory` | `/etc/docker/ssl` | TLS certificate directory |
| `docker_systemd_unit_directory` | `/etc/systemd/system/docker.service.d` | systemd unit override directory |

### Features

| Variable | Required | Description |
| --- | --- | --- |
| `docker_min_api_version` | No | Sets `DOCKER_MIN_API_VERSION` on the Docker daemon, restricting clients to this version or higher |
| `docker_tcp_port` | No | TCP port to expose the Docker daemon on. All four TCP variables must be set to enable the TCP socket |
| `docker_tcp_ca` | No | CA certificate (PEM) used to authenticate clients |
| `docker_tcp_cert` | No | Server certificate (PEM) |
| `docker_tcp_key` | No | Server private key (PEM) |

The TCP socket is enabled only when `docker_tcp_port`, `docker_tcp_ca`, `docker_tcp_cert`, and `docker_tcp_key` are all defined. The socket is mutually TLS-authenticated: the daemon verifies client certificates against `docker_tcp_ca`, and clients must verify the server certificate.

## Dependencies

None.

## Example playbook

Minimal install:

```yaml
- hosts: all
  roles:
    - stephdewit.docker
```

With a TLS TCP socket:

```yaml
- hosts: all
  roles:
    - role: stephdewit.docker
      vars:
        docker_apt_repository:
          distribution: bookworm
        docker_tcp_port: 2376
        docker_tcp_ca: "{{ lookup('file', 'ca.pem') }}"
        docker_tcp_cert: "{{ lookup('file', 'server-cert.pem') }}"
        docker_tcp_key: "{{ lookup('file', 'server-key.pem') }}"
```

## License

BSD
