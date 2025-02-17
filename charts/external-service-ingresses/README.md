# External Service Ingresses Helm Chart

This Helm chart creates Kubernetes ingresses and external name services to route traffic to external services outside your cluster.

## Overview

This chart allows you to define multiple external services and their corresponding ingress rules in a declarative way. It's particularly useful when you need to:

- Route traffic to external services through your cluster's ingress controller
- Create multiple ingress rules for the same external service
- Manage external service configurations in a Kubernetes-native way

## Installation

```bash
helm repo add zac-pizza https://zac.pizza/helm-charts
helm install external-services zac-pizza/external-service-ingresses
```

## Configuration

### Values.yaml Example

```yaml
services:
  - name: example-service
    external_name: 192.30.252.153
    service_ports:
      - port: 80
        target_port: 80
        name: http
        protocol: TCP
    ingresses:
      - host: service.mydomain.com
        port: 80
        ingressClassName: internal
```

### Configuration Parameters

#### Service Configuration

| Parameter                                | Description           | Required | Default |
| ---------------------------------------- | --------------------- | -------- | ------- |
| `services[].name`                        | Name of the service   | Yes      |         |
| `services[].external_name`               | External service FQDN | Yes      |         |
| `services[].service_ports`               | List of service ports | Yes      |         |
| `services[].service_ports[].port`        | Port number           | Yes      |         |
| `services[].service_ports[].target_port` | Target port number    | Yes      |         |
| `services[].service_ports[].name`        | Port name             | Yes      |         |
| `services[].service_ports[].protocol`    | Protocol              | No       | `TCP`   |

#### Ingress Configuration

| Parameter                                 | Description                      | Required | Default                  |
| ----------------------------------------- | -------------------------------- | -------- | ------------------------ |
| `services[].ingresses[].host`             | Hostname for the ingress rule    | Yes      |                          |
| `services[].ingresses[].path`             | Path for the ingress rule        | No       | `"/"`                    |
| `services[].ingresses[].pathType`         | Kubernetes ingress path type     | No       | `ImplementationSpecific` |
| `services[].ingresses[].port`             | Service port to route traffic to | Yes      |                          |
| `services[].ingresses[].ingressClassName` | Ingress controller class         | Yes      |                          |
| `services[].ingresses[].annotations`      | Ingress annotations              | No       |                          |

## Generated Resources

For each service defined in the values, this chart will create:

1. An ExternalName service that points to the external service
2. One or more ingress rules based on the ingress configurations

## Examples

### Single Service with Multiple Ingress Rules

services:

- name: api-service
  external_name: api.external-service.com
  service_ports:
  - port: 80
    target_port: 80
    name: http
    ingresses:
  - host: api.mydomain.com
    ingressClassName: nginx
    port: 80
  - host: api-v2.mydomain.com
    path: /v2
    ingressClassName: nginx
    port: 80
    annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /

## Notes

- The chart requires Kubernetes 1.19+ due to the use of networking.k8s.io/v1 API for Ingress resources
- Make sure your cluster has an appropriate ingress controller installed
- External services must be accessible from your cluster

## Version History

- 1.0.1: Current version

## License

This Helm chart is available under the MIT license.
