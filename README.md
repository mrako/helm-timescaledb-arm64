# TimescaleDB Helm Config for ARM64

This repository describes installing [TimescaleDB](https://www.timescale.com/) on a Raspberry PI using [Kubernetes](https://kubernetes.io/) / [k3s](https://k3s.io/) and [Helm](https://helm.sh/).

## Prerequisites

* [k3s](https://docs.k3s.io/installation)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)
* [Helm](https://helm.sh/docs/intro/install/)


## Configuration

Create a configuration file `config.yml` to use the ARM64-supported image:

```
image:
  repository: timescaledev/timescaledb-ha
  tag: pg15-multi-all-arm64
  pullPolicy: Always
```

## Installation

```bash
helm repo add timescale 'https://charts.timescale.com'
helm repo update
helm install timescale -f config.yml timescale/timescaledb-single
```

## Accessing DB

```bash
PGPASSWORD_POSTGRES=$(kubectl get secret --namespace default "timescale-credentials" -o jsonpath="{.data.PATRONI_SUPERUSER_PASSWORD}" | base64 --decode)

kubectl run -i --tty --rm psql --image=postgres \
  --env "PGPASSWORD=$PGPASSWORD_POSTGRES" \
  --command -- psql -U postgres \
  -h timescale.default.svc.cluster.local postgres
```
