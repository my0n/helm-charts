# my0n/danbooru

An unofficial helm chart for [danbooru](https://github.com/danbooru/danbooru).

**This chart is not maintained by the upstream project and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install danbooru my0n/danbooru -f values.yaml
```

See [values.yaml](values.yaml) for more details.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| args | string[] | `["bash", "-c", "bin/rails db:prepare && bin/rails db:seed && bin/rails server -b 0.0.0.0"]` |  |
| cron | object | See values.yaml | Configuration for the cron task |
| env | string | See values.yaml | environment variables. See [image docs](https://docs.k8s-at-home.com/our-container-images/configuration/) for more details. |
| image.pullPolicy | string | `"IfNotPresent"` | image pull policy |
| image.repository | string | `"ghcr.io/danbooru/danbooru"` | image repository |
| image.tag | string | `nil` |  |
| ingress.main | object | See values.yaml | Enable and configure ingress settings for the chart under this key. |
| jobs | object | See values.yaml | Configuration for the jobs task |
| persistence.data.accessMode | string | `"ReadWriteOnce"` |  |
| persistence.data.enabled | bool | `false` |  |
| persistence.data.mountPath | string | `"/danbooru/public/data"` |  |
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
