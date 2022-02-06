# my0n/szurubooru-client

An unofficial helm chart for the [szurubooru](https://github.com/rr-/szurubooru) client.

**This chart is not maintained by the upstream project and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

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

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Defines affinity constraint rules. Read more about `affinity` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity). |
| autoscaling.enabled | bool | `false` | Enable the horizontal pod autoscaler. |
| autoscaling.maxReplicas | int | `100` | Max replicas for the horizontal pod autoscaler. |
| autoscaling.minReplicas | int | `1` | Min replicas for the horiziontal pod autoscaler. |
| autoscaling.targetCPUUtilizationPercentage | int | `80` | Target CPU utilization for the horizontal pod autoscaler. |
| backend.baseUrl | string | `"/"` | Path for the szurubooru-server service. Sets BASE_URL on the client pod. |
| backend.host | string | `"localhost"` | Host for the szurubooru-server service. Sets BACKEND_HOST on the client pod. |
| backend.maxFileSize | string | `"1073741824"` | Max file size for the szurubooru-server service. Accepts nginx values. Read more about valid nginx configuration syntax [here](http://nginx.org/en/docs/syntax.html). |
| backend.port | int | `6666` | Port for the szurubooru-server service. |
| fullnameOverride | string | `""` | Override for full name of generated resources. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy. |
| image.repository | string | `"szurubooru/client"` | Image repository. |
| image.tag | string | `""` | Image tag. |
| imagePullSecrets | list | `[]` | Secrets for pulling an image. |
| ingress.annotations | object | `{}` | Provide additional ingress annotations if needed. |
| ingress.enabled | bool | `false` | Enables or disables the ingress. |
| ingress.host | string | `"chart-example.local"` | Host address. |
| ingress.path | string | `"/"` | Path. |
| ingress.pathType | string | `"ImplementationSpecific"` | Ignored if not kubeVersion >= 1.18-0 |
| ingress.tls | list | `[]` | Configure TLS for the ingress. |
| nameOverride | string | `""` | Override for name of generated resources. |
| nodeSelector | object | `{}` | Defines node selection constraints. Read more about `nodeSelector` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector). |
| persistence.existingClaim | string | `""` | Use an existing Persistent Volume Claim. This should be the same one used by szurubooru-server; the two share this volume for images and thumbnails. |
| podAnnotations | object | `{}` | Annotations for the server pod. |
| podSecurityContext | object | `{}` | Pod security context. |
| replicaCount | int | `1` | Number of desired pods. |
| resources | object | `{}` | Set the resource limits/requests for the pod. |
| securityContext | object | `{}` | Security context. |
| service.port | int | `80` | The port for the service. |
| service.type | string | `"ClusterIP"` | The type of service. |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account. |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created. |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and `serviceAccount.create` is true, a name is generated using the fullname template. |
| tolerations | list | `[]` | Specify taint tolerations. Read more about `tolerations` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). |
