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
    external_ip: 192.30.252.153
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
| `services[].external_ip`                 | External service IP   | Yes      |         |
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

1. A ClusterIP service with an Endpoints resource pointing to the external IP for each of the ports defined in the service_ports section
2. One or more ingress rules based on the ingress configurations

## Examples

### Single Service with Multiple ports and ingresses

```yaml
services:
  - name: api-service
    external_ip: 192.30.252.153
    service_ports:
      - port: 80
        target_port: 80
        name: frontend
      - port: 8022
        target_port: 8022
        name: admin
    ingresses:
      - host: frontend.mydomain.com
        ingressClassName: internal
        port: 80
      - host: admin.mydomain.com
        ingressClassName: internal
        port: 8022
```

## Notes

- The chart requires Kubernetes 1.19+
- Make sure your cluster has an appropriate ingress controller installed
- External services must be accessible from your cluster

## Version History

- 2.0.0: Switched to ClusterIP service type and Endpoints resource (aka the correct way to do it)
- 1.0.1: Fixed issue with service name in ingress template
- 1.0.0: Initial version

## License

This Helm chart is available under the MIT license.
