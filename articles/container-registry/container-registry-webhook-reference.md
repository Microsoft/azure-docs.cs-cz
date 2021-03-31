---
title: Referenční dokumentace schématu Webhooku registru
description: Referenční informace pro datovou část JSON pro žádosti Webhooku v registru kontejnerů Azure, které se generují, když jsou Webhooky povolené pro události push a DELETE artefaktu
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74455972"
---
# <a name="azure-container-registry-webhook-reference"></a>Reference k Azure Container Registry Webhooku

U svého registru kontejneru můžete [nakonfigurovat Webhooky](container-registry-webhook.md) , které generují události při provádění určitých akcí. Můžete například povolit Webhooky, které se aktivují, když se do registru vloží obrázek kontejneru nebo Helm graf, nebo se odstraní. Při aktivaci Webhooku Azure Container Registry vydá požadavek HTTP nebo HTTPS obsahující informace o události do zadaného koncového bodu. Koncový bod pak může tento Webhook zpracovat a odpovídajícím způsobem reagovat.

Následující části podrobně popisují schéma požadavků webhooků generovaných podporovanými událostmi. Oddíly události obsahují schéma datové části pro typ události, ukázkovou datovou část požadavku a jeden nebo více ukázkových příkazů, které by aktivovaly Webhook.

Informace o konfiguraci webhooků pro váš registr kontejneru Azure najdete v tématu [použití Azure Container Registry webhooků](container-registry-webhook.md).

## <a name="webhook-requests"></a>Žádosti Webhooku

### <a name="http-request"></a>Požadavek HTTP

Aktivovaný Webhook vytvoří `POST` požadavek HTTP na koncový bod adresy URL, který jste zadali při konfiguraci Webhooku.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Žádosti Webhooku zahrnují `Content-Type` , `application/json` Pokud jste `Content-Type` pro Webhook nezadali vlastní hlavičku.

Do žádosti nejsou přidána žádná další záhlaví nad rámec těchto vlastních hlaviček, které jste mohli pro Webhook zadat.

## <a name="push-event"></a>Událost push

Webhook aktivovaný při vložení image kontejneru do úložiště

### <a name="push-event-payload"></a>Datová část události push

|Prvek|Typ|Description|
|-------------|----------|-----------|
|`id`|Řetězec|ID události Webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost Webhooku.|
|`action`|Řetězec|Akce, která aktivovala událost Webhooku.|
|[cílové](#target)|Komplexní typ|Cíl události, která aktivovala událost Webhooku.|
|[Request](#request)|Komplexní typ|Požadavek, který vygeneroval událost Webhooku.|

### <a name="target"></a><a name="target"></a>cílové

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu|
|`size`|Int32|Počet bajtů obsahu. Stejné jako pole length.|
|`digest`|Řetězec|Výtah obsahu definovaný specifikací registru v2 HTTP API.|
|`length`|Int32|Počet bajtů obsahu. Stejné jako velikost pole.|
|`repository`|Řetězec|Název úložiště|
|`tag`|Řetězec|Název značky obrázku|

### <a name="request"></a><a name="request"></a>Request

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`id`|Řetězec|ID žádosti, která iniciovala událost.|
|`host`|Řetězec|Externě přístupný název hostitele instance registru, jak je určen hlavičkou hostitele HTTP na příchozích požadavcích.|
|`method`|Řetězec|Metoda žádosti, která vygenerovala událost.|
|`useragent`|Řetězec|Záhlaví uživatelského agenta požadavku.|

### <a name="payload-example-image-push-event"></a>Příklad datové části: událost push obrázku

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Příklad příkazu [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) , který spustí Webhook události **push** Image:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Událost push grafu

Webhook aktivovaný při vložení grafu Helm do úložiště

### <a name="chart-push-event-payload"></a>Datová část události nabízeného grafu

|Prvek|Typ|Description|
|-------------|----------|-----------|
|`id`|Řetězec|ID události Webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost Webhooku.|
|`action`|Řetězec|Akce, která aktivovala událost Webhooku.|
|[cílové](#helm_target)|Komplexní typ|Cíl události, která aktivovala událost Webhooku.|

### <a name="target"></a><a name="helm_target"></a>cílové

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|Řetězec|Výtah obsahu definovaný specifikací registru v2 HTTP API.|
|`repository`|Řetězec|Název úložiště|
|`tag`|Řetězec|Název značky grafu|
|`name`|Řetězec|Název grafu|
|`version`|Řetězec|Verze grafu|

### <a name="payload-example-chart-push-event"></a>Příklad datové části: událost push grafu

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Příklad příkazu rozhraní příkazového [řádku Azure](/cli/azure/acr) , který aktivuje Webhook události **chart_push** :

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Odstranit událost

Webhook aktivovaný při odstranění úložiště imagí nebo manifestu Neaktivuje se při odstranění značky.

### <a name="delete-event-payload"></a>Odstranit datovou část události

|Prvek|Typ|Description|
|-------------|----------|-----------|
|`id`|Řetězec|ID události Webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost Webhooku.|
|`action`|Řetězec|Akce, která aktivovala událost Webhooku.|
|[cílové](#delete_target)|Komplexní typ|Cíl události, která aktivovala událost Webhooku.|
|[Request](#delete_request)|Komplexní typ|Požadavek, který vygeneroval událost Webhooku.|

### <a name="target"></a><a name="delete_target"></a> cílové

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu|
|`digest`|Řetězec|Výtah obsahu definovaný specifikací registru v2 HTTP API.|
|`repository`|Řetězec|Název úložiště|

### <a name="request"></a><a name="delete_request"></a> Request

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`id`|Řetězec|ID žádosti, která iniciovala událost.|
|`host`|Řetězec|Externě přístupný název hostitele instance registru, jak je určen hlavičkou hostitele HTTP na příchozích požadavcích.|
|`method`|Řetězec|Metoda žádosti, která vygenerovala událost.|
|`useragent`|Řetězec|Záhlaví uživatelského agenta požadavku.|

### <a name="payload-example-image-delete-event"></a>Příklad datové části: událost odstranění obrázku

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Příklady příkazů rozhraní příkazového [řádku Azure](/cli/azure/acr) , které aktivují Webhook události **Delete** :

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Událost odstranění grafu

Webhook aktivovaný při odstranění grafu Helm nebo úložiště 

### <a name="chart-delete-event-payload"></a>Datová část události odstranění grafu

|Prvek|Typ|Description|
|-------------|----------|-----------|
|`id`|Řetězec|ID události Webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost Webhooku.|
|`action`|Řetězec|Akce, která aktivovala událost Webhooku.|
|[cílové](#chart_delete_target)|Komplexní typ|Cíl události, která aktivovala událost Webhooku.|

### <a name="target"></a><a name="chart_delete_target"></a> cílové

|Prvek|Typ|Description|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|Řetězec|Výtah obsahu definovaný specifikací registru v2 HTTP API.|
|`repository`|Řetězec|Název úložiště|
|`tag`|Řetězec|Název značky grafu|
|`name`|Řetězec|Název grafu|
|`version`|Řetězec|Verze grafu|

### <a name="payload-example-chart-delete-event"></a>Příklad datové části: událost odstranění grafu

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Příklad příkazu rozhraní příkazového [řádku Azure](/cli/azure/acr) , který aktivuje Webhook události **chart_delete** :

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Další kroky

[Používání Azure Container Registry webhooků](container-registry-webhook.md)