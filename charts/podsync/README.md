# my0n/podsync

An unofficial helm chart for [podsync](https://github.com/mxpv/podsync) (currently using [this version](https://github.com/tuxpeople/docker-podsync) for yt-dlp support).

**This chart is not maintained by the upstream project and any issues with the chart should be raised [here](https://github.com/my0n/helm-charts/issues/new).**

## Example usage

Install the helm chart:

```sh
helm repo add my0n https://my0n.github.io/helm-charts
helm repo update
helm install podsync my0n/podsync -f values.yaml
```

Sample values.yaml given below; see [values.yaml](values.yaml) for more details.

```yaml
# values.yaml
configuration:
  template: |
    [server]
    hostname = "http://${PODSYNC_HOST}"
    port = ${PODSYNC_PORT}
    data_dir = "${PODSYNC_DATA_DIR}"

    [tokens]
    youtube = "${PODSYNC_YOUTUBE_KEY}"

    [feeds]
      [feeds.ID1]
      url = "https://www.youtube.com/channel/UCxC5Ls6DwqV0e-CYcAKkExQ"
  env:
    - name: PODSYNC_YOUTUBE_KEY
      valueFrom:
        secretKeyRef:
          name: youtube-api-key
          key: apiKey
persistence:
  enabled: true
  size: 100Gi
ingress:
  enabled: true
  host: podsync.example.local
```

Example kubernetes secret:

```yaml
# youtube-api-key.yaml
apiVersion: v1
kind: Secret
metadata:
  name: youtube-api-key
  namespace: default
type: Opaque
data:
  apiKey: WU9VUl9ZT1VUVUJFX0FQSV9LRVlfSEVSRQ==
```

## Integrating with Vault

The above example shows how to use a plain ol' secret with the configuration template. Here's how you do it with Vault (disclaimer, I'm not an expert at this):

First, create a new secret for your secret. We'll use a youtube API key as an example.

```sh
vault kv put internal/youtube/config apiKey="abcdefg"
```

Next, set up a new policy that the podsync service account can use to read the secret.

```sh
vault policy write podsync - <<EOF
path "internal/data/youtube/config" {
  capabilities = ["read"]
}
EOF
```

Then, give the service account access to that policy. By default, the service account will be called "podsync" but you can overwrite this with the value `serviceAccount.name`.

```sh
vault write auth/kubernetes/role/podsync \
  bound_service_account_names=podsync \
  bound_service_account_namespaces=default \
  policies=podsync \
  ttl=24h
```

Finally, add the following to your values.yaml (adjusted as needed).

```yaml
configuration:
  envInjectSource: /vault/secrets/config
podAnnotations:
  vault.hashicorp.com/agent-inject: 'true'
  vault.hashicorp.com/role: 'podsync'
  vault.hashicorp.com/agent-init-first: 'true'
  vault.hashicorp.com/agent-inject-secret-config: 'internal/data/youtube/config'
  vault.hashicorp.com/agent-inject-template-config: |
    {{ with secret "internal/data/youtube/config" -}}
      export PODSYNC_YOUTUBE_KEY="{{ .Data.data.apiKey }}"
    {{- end }}
```

And that's it! I hope!

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Defines affinity constraint rules. Read more about `affinity` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity). |
| configuration.env | list | `[]` | A list of environment variables to use in `configuration.template`. See example above. |
| configuration.envInjectSource | string | `""` | File name of a shell script to load additional template environment variables from. This is useful when using Vault - the initContainer will first `source` the file you provide to load env variables, then it will run `envsubst` on the template with both the variables you specify in `configuration.env` and the variables from this file. |
| configuration.template | string | `""` | Template for an automatically generated config file. Environment variables wrapped in `${}` will be replaced. See example above. A more complete example of the config file can be found on the podsync repo [here](https://github.com/mxpv/podsync/blob/main/config.toml.example). |
| fullnameOverride | string | `""` | Override for full name of generated resources. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy. |
| image.repository | string | `"tdeutsch/podsync"` | Image repository. |
| image.tag | string | `""` | Image tag. |
| imagePullSecrets | list | `[]` | Secrets for pulling an image. |
| ingress.annotations | object | `{}` | Provide additional ingress annotations if needed. |
| ingress.className | string | `""` | Ingress class name. |
| ingress.enabled | bool | `false` | Enables or disables the ingress. |
| ingress.host | string | `"chart-example.local"` | Host address. |
| ingress.paths[0].path | string | `"/"` | Path. |
| ingress.paths[0].pathType | string | `"ImplementationSpecific"` | Ignored if not kubeVersion >= 1.18-0 |
| ingress.tls | list | `[]` | Configure TLS for the ingress. |
| nameOverride | string | `""` | Override for name of generated resources. |
| nodeSelector | object | `{}` | Defines node selection constraints. Read more about `nodeSelector` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector). |
| persistence.accessModes[0] | string | `"ReadWriteOnce"` | Access mode for generated Persistent Volume Claim. |
| persistence.annotations | object | `{}` | Annotations for the generated Persistent Volume Claim. |
| persistence.enabled | bool | `false` | If enabled, a Persistent Volume Claim will either be created or used. If not enabled, podsync just uses an emptyDir. |
| persistence.existingClaim | string | `""` | Use an existing Persistent Volume Claim. If empty string (default), then a new one will be generated. |
| persistence.size | string | `"10Gi"` | The size of the generated Persistent Volume Claim. |
| persistence.storageClass | string | `""` | The storage class for the generated Persistent Volume Claim. If empty string (default), then the default provisioner will be used. |
| podAnnotations | object | `{}` | Annotations for the server pod. |
| podSecurityContext | object | `{}` | Pod security context. |
| resources | object | `{}` | Set the resource limits/requests for the pod. |
| securityContext | object | `{}` | Security context. |
| service.port | int | `80` | The port for the service. |
| service.type | string | `"ClusterIP"` | The type of service. |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account. |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created. |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and `serviceAccount.create` is true, a name is generated using the fullname template. |
| tolerations | list | `[]` | Specify taint tolerations. Read more about `tolerations` [here](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). |
