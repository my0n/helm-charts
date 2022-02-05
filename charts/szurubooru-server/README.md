# my0n/szurubooru-server

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

## Values.yaml reference

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Defines affinity constraint rules. Read more about `affinity` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity). |
| autoscaling.enabled | bool | `false` | Enable the horizontal pod autoscaler. |
| autoscaling.maxReplicas | int | `100` | Max replicas for the horizontal pod autoscaler. |
| autoscaling.minReplicas | int | `1` | Min replicas for the horiziontal pod autoscaler. |
| autoscaling.targetCPUUtilizationPercentage | int | `80` | Target CPU utilization for the horizontal pod autoscaler. |
| configuration.enabled | bool | `true` | If enabled, provide the config.yaml file to the server. |
| configuration.template | string | `""` | Template for an automatically generated config file. Environment variables wrapped in `${}` will be replaced. See example above. A more complete example of the config file can be found on the szurubooru repo [here](https://github.com/rr-/szurubooru/blob/master/server/config.yaml.dist). |
| configuration.templateEnv | list | `[]` | A list of environment variables to use in `configuration.templateEnv`. See example above. |
| fullnameOverride | string | `""` | Override for full name of generated resources. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy. |
| image.repository | string | `"szurubooru/server"` | Image repository. |
| image.tag | string | `""` | Image tag. |
| imagePullSecrets | list | `[]` | Secrets for pulling an image. |
| ingress.annotations | object | `{}` | Provide additional ingress annotations if needed. |
| ingress.enabled | bool | `false` | Enables or disables the ingress. For the server, this should be left set to `false` unless you want direct access to the API. |
| ingress.host | string | `"chart-example.local"` | Host address. |
| ingress.path | string | `"/"` | Path. |
| ingress.pathType | string | `"ImplementationSpecific"` | Ignored if not kubeVersion >= 1.18-0 |
| ingress.tls | list | `[]` | Configure TLS for the ingress. |
| nameOverride | string | `""` | Override for name of generated resources. |
| nodeSelector | object | `{}` | Defines node selection constraints. Read more about `nodeSelector` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector). |
| persistence.accessModes[0] | string | `"ReadWriteOnce"` | Access mode for generated Persistent Volume Claim. |
| persistence.annotations | object | `{}` | Annotations for the generated Persistent Volume Claim. |
| persistence.enabled | bool | `false` | If enabled, a Persistent Volume Claim will either be created or used. If not enabled, the server just uses an emptyDir. |
| persistence.existingClaim | string | `""` | Use an existing Persistent Volume Claim. If empty string (default), then a new one will be generated. |
| persistence.size | string | `"10Gi"` | The size of the generated Persistent Volume Claim. |
| persistence.storageClass | string | `""` | The storage class for the generated Persistent Volume Claim. If empty string (default), then the default provisioner will be used. |
| podAnnotations | object | `{}` | Annotations for the server pod. |
| podSecurityContext | object | `{}` | Pod security context. |
| postgres.db | string | `"szurubooru"` | PostgreSQL database. Sets POSTGRES_DB on the server pod. |
| postgres.host | string | `""` | PostgreSQL host. Sets POSTGRES_HOST on the server pod. |
| postgres.password | string | `""` | PostgreSQL password (overrides `postgres.passwordSecret`). Sets POSTGRES_PASSWORD on the server pod. |
| postgres.passwordSecret | object | `{}` | Existing secret for the PostgreSQL password. Sets POSTGRES_PASSWORD on the server pod. |
| postgres.port | int | `5432` | PostgreSQL port. Sets POSTGRES_PORT on the server pod. |
| postgres.user | string | `""` | PostgreSQL username (overrides `postgres.userSecret`). Sets POSTGRES_USER on the server pod. |
| postgres.userSecret | object | `{}` | Existing secret for the PostgreSQL username. Sets POSTGRES_USER on the server pod. |
| postgres.verboseLogs | bool | `false` | Enable verbose SQL logs. Sets LOG_SQL on the server pod. |
| replicaCount | int | `1` | Number of desired pods. |
| resources | object | `{}` | Set the resource limits/requests for the pod. |
| securityContext | object | `{}` | Security context. |
| service.port | int | `6666` | The port for the service. |
| service.type | string | `"ClusterIP"` | The type of service. |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account. |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created. |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and `serviceAccount.create` is true, a name is generated using the fullname template. |
| tolerations | list | `[]` | Specify taint tolerations. Read more about `tolerations` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). |
