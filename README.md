# akrobateo

akrobateo is a Go-based Kubernetes operator that exposes `LoadBalancer` services through node `hostPort`s using `DaemonSet`s. It provides a way to expose these services in clusters without a cloud-provider load balancer implementation and updates their service addresses.

## Features

- Watch Kubernetes services with `type: LoadBalancer`.
- Create `DaemonSet`s to expose service ports on nodes through `hostPort`s.
- Forward traffic using iptables in the proxy Pods.
- Synchronize load balancer addresses on the services.
- Accept a custom proxy image through the `LB_IMAGE` environment variable.

## Installation

From a local checkout, with Go installed, build the project using the included script:

```sh
./build.sh
```

A `Dockerfile` is also included for building a container image.

To install the operator in a Kubernetes cluster, apply the manifests in `deploy` using a configured `kubectl`:

```sh
kubectl apply -f deploy/
```

The deployment manifests place the operator in `kube-system` and configure its service account and RBAC permissions.

## Usage

For local development, build the project and run the binary against your configured Kubernetes cluster. For example, on macOS with an AMD64 processor, watch the `default` namespace:

```sh
WATCH_NAMESPACE="default" ./output/akrobateo_darwin_amd64
```

Alternatively, with the project's compatible Operator SDK installed, run:

```sh
operator-sdk up local
```

Create a service with `type: LoadBalancer` in the watched namespace. The operator creates a proxy `DaemonSet` and synchronizes the service's load balancer addresses. Set `LB_IMAGE` in the operator's environment to use a custom proxy image.

Because the proxy Pods use node `hostPort`s, a given port can be exposed by only one service across the cluster. This approach does not provision an external load balancer; clients connect through the node addresses.

## License

Licensed under Apache-2.0. This project is based on [kontena/akrobateo](https://github.com/kontena/akrobateo), also licensed under Apache-2.0. Retain the upstream license and applicable attribution notices when redistributing the code.
