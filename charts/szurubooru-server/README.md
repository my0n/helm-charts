# my0n/szurubooru-client

An unofficial helm chart for the [szurubooru](https://github.com/rr-/szurubooru) server.

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install szurubooru-server my0n/szurubooru-server -f values.yaml
```

Sample values.yaml given below; see [values.yaml](values.yaml) for more details.

```yaml
# values.yaml
configuration:
  enabled: true
  template: |
    name: My Self-Hosted Szurubooru
    domain: http://${SZURUBOORU_HOST}
    secret: ${SZURUBOORU_SECRET}
    max_dl_filesize: 10.0E+12
  templateEnv:
    - name: SZURUBOORU_SECRET
      valueFrom:
        secretKeyRef:
          name: szurubooru-server-salt
          key: salt
persistence:
  enabled: true
  size: 10Gi
postgres:
  db: postgres
  host: postgresql.default.svc
  port: 5432
  userSecret:
    name: postgresql-credentials
    key: user
  passwordSecret:
    name: postgresql-credentials
    key: password
  verboseLogs: false
podSecurityContext:
  runAsUser: 2000
  runAsGroup: 2000
  fsGroup: 2000
```

Example kubernetes secret:

```yaml
# szurubooru-server-salt.yaml
apiVersion: v1
kind: Secret
metadata:
  name: szurubooru-server-salt
  namespace: default
type: Opaque
data:
  salt: dmVyeXNlY3JldHNhbHQ=
```
