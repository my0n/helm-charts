# my0n/szurubooru-client

An unofficial helm chart for the [szurubooru](https://github.com/rr-/szurubooru) client.

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install szurubooru-client my0n/szurubooru-client -f values.yaml
```

Sample values.yaml given below; see [values.yaml](values.yaml) for more details.

```yaml
# values.yaml
backend:
  host: szurubooru-server.default.svc
  maxFileSize: 1000M
persistence:
  existingClaim: szurubooru-server
ingress:
  enabled: true
  host: szurubooru.example.local
```
