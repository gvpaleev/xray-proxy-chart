# Xray Proxy Helm Chart

SOCKS5-прокси на [Xray-core](https://github.com/XTLS/Xray-core) с исходящим VLESS+Reality, упакованный в Helm-чарт для Kubernetes.

OCI-репозиторий: `oci://ghcr.io/gvpaleev/xray-proxy`
Образ: [`docker.io/gvpaleevkz/xray-proxy`](https://hub.docker.com/repository/docker/gvpaleevkz/xray-proxy/general)

## Быстрая установка из OCI

```bash
helm upgrade --install xray-proxy oci://ghcr.io/gvpaleev/xray-proxy \
  --set xray.vless.userId=<VLESS_ID> \
  --set xray.reality.publicKey=<PUBLIC_KEY>
```

## Локальная установка

```bash
helm upgrade --install xray-proxy ./chart \
  --set xray.vless.userId=<VLESS_ID> \
  --set xray.reality.publicKey=<PUBLIC_KEY>
```

## Установка с NodePort (внешний доступ)

```bash
helm upgrade --install xray-proxy oci://ghcr.io/gvpaleev/xray-proxy \
  --set service.type=NodePort \
  --set service.nodePort=31080 \
  --set xray.vless.userId=<VLESS_ID> \
  --set xray.reality.publicKey=<PUBLIC_KEY>
```

## Проверка

```bash
kubectl port-forward svc/xray-proxy 1080:1080
curl -x socks5h://127.0.0.1:1080 https://httpbin.org/ip
```

## Artifact Hub

В Artifact Hub добавить как OCI-репозиторий:
`oci://ghcr.io/gvpaleev/xray-proxy`

Для статуса Verified Publisher — после регистрации репозитория запушить `artifacthub-repo.yml` с `repositoryID`:

```bash
regctl artifact put \
  --artifact-type application/vnd.cncf.artifacthub.config.v1+yaml \
  -f artifacthub-repo.yml \
  --file-media-type "application/vnd.cncf.artifacthub.repository-metadata.layer.v1.yaml" \
  ghcr.io/gvpaleev/xray-proxy:artifacthub.io
```

## Параметры

### Image

| Параметр            | По умолчанию                           | Описание                 |
|---------------------|----------------------------------------|--------------------------|
| `image.repository`  | `docker.io/gvpaleevkz/xray-proxy`      | Docker-образ             |
| `image.tag`         | `""` (appVersion чарта)                | Тег образа               |
| `image.pullPolicy`  | `Always`                               | Политика загрузки образа |

### Xray (VLESS+Reality)

| Параметр                        | По умолчанию      | Описание                            |
|---------------------------------|-------------------|-------------------------------------|
| `xray.logLevel`                 | `warning`         | Уровень логирования                 |
| `xray.dns.servers`              | `[1.1.1.1, 8.8.8.8]` | DNS-серверы                    |
| `xray.socksPort`                | `1080`            | Порт SOCKS5                         |
| `xray.vless.address`            | `213.217.31.253`  | Адрес VLESS-сервера                 |
| `xray.vless.port`               | `443`             | Порт VLESS-сервера                  |
| `xray.vless.userId`             | `REPLACE_ME`      | **Обязательно.** VLESS UUID         |
| `xray.vless.encryption`         | `none`            | Шифрование                          |
| `xray.vless.flow`               | `xtls-rprx-vision`| Flow                                |
| `xray.reality.serverName`       | `cm.a.mts.ru`     | SNI для Reality                     |
| `xray.reality.fingerprint`      | `ios`             | Fingerprint                         |
| `xray.reality.publicKey`        | `REPLACE_ME`      | **Обязательно.** Reality public key |
| `xray.reality.shortId`          | `01`              | Short ID                            |
| `xray.reality.spiderX`          | `""`              | SpiderX                             |

### Service

| Параметр              | По умолчанию | Описание                  |
|-----------------------|--------------|---------------------------|
| `service.type`        | `ClusterIP`  | Тип сервиса               |
| `service.port`        | `1080`       | Порт SOCKS5               |
| `service.nodePort`    | `""`         | Фиксированный NodePort    |

### Resources

| Параметр                    | По умолчанию | Описание          |
|-----------------------------|--------------|-------------------|
| `resources.requests.cpu`    | `10m`        | CPU request       |
| `resources.requests.memory` | `32Mi`       | Memory request    |
| `resources.limits.memory`   | `128Mi`      | Memory limit      |

## Лицензия

MIT
