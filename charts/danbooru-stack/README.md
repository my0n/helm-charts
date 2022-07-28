# my0n/danbooru-stack

An unofficial helm chart for the entire [danbooru](https://github.com/danbooru/danbooru) stack. This chart packages the following subcharts into one unit:
* [my0n/danbooru](https://github.com/my0n/helm-charts/blob/master/charts/danbooru)
* [my0n/iqdb](https://github.com/my0n/helm-charts/blob/master/charts/iqdb)
* [bitnami/postgresql](https://github.com/bitnami/charts/blob/master/bitnami/postgresql)
* [bitnami/redis](https://github.com/bitnami/charts/blob/master/bitnami/redis)

**This chart is not maintained by those upstream projects and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install danbooru-stack my0n/danbooru-stack -f values.yaml
```

See [values.yaml](values.yaml) for more details.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| danbooru.enabled | bool | `true` |  |
| iqdb.enabled | bool | `true` |  |
| postgresql.architecture | string | `"standalone"` |  |
| postgresql.enabled | bool | `true` |  |
| redis.architecture | string | `"standalone"` |  |
| redis.enabled | bool | `true` |  |
