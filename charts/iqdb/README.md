# my0n/iqdb

An unofficial helm chart for [iqdb](https://github.com/danbooru/iqdb).

**This chart is not maintained by the upstream project and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install iqdb my0n/iqdb -f values.yaml
```

See [values.yaml](values.yaml) for more details.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| args | string[] | `["http", "0.0.0.0", "5588", "/iqdb/data/iqdb.sqlite"]` |  |
| image.pullPolicy | string | `"IfNotPresent"` | image pull policy |
| image.repository | string | `"evazion/iqdb"` | image repository |
| image.tag | string | `nil` |  |
| persistence.data.accessMode | string | `"ReadWriteOnce"` |  |
| persistence.data.enabled | bool | `false` |  |
| persistence.data.mountPath | string | `"/iqdb/data"` |  |
| persistence.data.size | string | `"10Gi"` |  |
| persistence.data.type | string | `"pvc"` |  |
| podSecurityContext.fsGroup | int | `568` |  |
| podSecurityContext.runAsGroup | int | `568` |  |
| podSecurityContext.runAsNonRoot | bool | `true` |  |
| podSecurityContext.runAsUser | int | `568` |  |
| securityContext.allowPrivilegeEscalation | bool | `false` |  |
| securityContext.runAsGroup | int | `568` |  |
| securityContext.runAsUser | int | `568` |  |
| service | object | See values.yaml | Configures service settings for the chart. |
