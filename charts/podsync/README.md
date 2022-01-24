# my0n/podsync

An unofficial helm chart for [podsync](https://github.com/mxpv/podsync) (currently using [this version](https://github.com/tuxpeople/docker-podsync) for yt-dlp support).

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
