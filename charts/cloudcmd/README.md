# my0n/cloudcmd

An unofficial helm chart for [cloudcmd](https://github.com/coderaiser/cloudcmd).

**This chart is not maintained by the upstream project and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install cloudcmd my0n/cloudcmd -f values.yaml
```

See [values.yaml](values.yaml) for more details.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| env | string | See values.yaml | environment variables. See [image docs](https://docs.k8s-at-home.com/our-container-images/configuration/) for more details. See https://cloudcmd.io/#environment-variables-1 for a list of supported environment variables. |
| image.pullPolicy | string | `"IfNotPresent"` | image pull policy |
| image.repository | string | `"coderaiser/cloudcmd"` | image repository |
| image.tag | string | `nil` |  |
| ingress.main | object | See values.yaml | Enable and configure ingress settings for the chart under this key. |
| persistence.data.enabled | bool | `false` |  |
| persistence.data.type | string | `"pvc"` |  |
| podSecurityContext | object | `{}` |  |
| securityContext | object | `{}` |  |
| service | object | See values.yaml | Configures service settings for the chart. |
