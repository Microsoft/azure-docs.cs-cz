---
title: Odkaz na schéma webhooku registru
description: Odkaz na datové části JSON pro požadavky webhooku v registru kontejnerů Azure, které se generují, když jsou povoleny webhooky pro události nabízených artefaktů nebo odstranění
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455972"
---
# <a name="azure-container-registry-webhook-reference"></a>Odkaz na webhooku registru kontejnerů Azure

Můžete [nakonfigurovat webhooky](container-registry-webhook.md) pro registr kontejneru, které generují události, když jsou prováděny určité akce proti němu. Povolte například webhooky, které se aktivují při nabízení bitové kopie kontejneru nebo grafu Helm do registru nebo odstranění. Když se aktivuje webhooku, Azure Container Registry vydá požadavek HTTP nebo HTTPS obsahující informace o události do zadaného koncového bodu. Koncový bod pak můžete zpracovat webhooku a podle toho jednat.

V následujících částech jsou podrobně popsány schéma požadavků webhooku generovaných podporovanými událostmi. Části událostí obsahují schéma datové části pro typ události, příklad datové části požadavku a jeden nebo více ukázkových příkazů, které by spustily webhook.

Informace o konfiguraci webhooků pro váš registr kontejnerů Azure najdete v tématu [Using Azure Container Registry webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Požadavky webhooku

### <a name="http-request"></a>Požadavek HTTP

Aktivovaný webhook vytvoří `POST` požadavek HTTP na koncový bod adresy URL, který jste zadali při konfiguraci webhooku.

### <a name="http-headers"></a>Hlavičky PROTOKOLU HTTP

Webhook požadavky `Content-Type` `application/json` zahrnují, pokud jste nezadali `Content-Type` vlastní záhlaví pro webhooku.

Do požadavku nejsou přidány žádné další hlavičky kromě vlastních záhlaví, která jste zadali pro webhook.

## <a name="push-event"></a>Nabízená událost

Webhook se aktivuje, když je bitová kopie kontejneru zasunuta do úložiště.

### <a name="push-event-payload"></a>Nákladová část nabízených událostí

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla spuštěna událost webhooku.|
|`action`|Řetězec|Akce, která spustila událost webhooku.|
|[Cíl](#target)|Komplexní typ|Cíl události, která spustila událost webhooku.|
|[Požadavek](#request)|Komplexní typ|Požadavek, který vygeneroval událost webhooku.|

### <a name="target"></a><a name="target"></a>Cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu. Stejné jako pole Length.|
|`digest`|Řetězec|Digest obsahu, jak je definováno v registru V2 HTTP API specifikace.|
|`length`|Int32|Počet bajtů obsahu. Stejné jako pole Velikost.|
|`repository`|Řetězec|Název úložiště.|
|`tag`|Řetězec|Název značky obrázku.|

### <a name="request"></a><a name="request"></a>Požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, který inicioval událost.|
|`host`|Řetězec|Externě přístupný název hostitele instance registru, jak je určeno hlavičkou hostitele HTTP na příchozí požadavky.|
|`method`|Řetězec|Metoda požadavku, která vygenerovala událost.|
|`useragent`|Řetězec|Hlavička uživatelského agenta požadavku.|

### <a name="payload-example-image-push-event"></a>Příklad datové části: událost nabízení obrázků

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

Příklad [příkazu Cli dockeru,](https://docs.docker.com/engine/reference/commandline/cli/) který aktivuje webhooku **události push** image:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Událost nabízení grafu

Webhook se aktivuje, když je graf Helm zasunut do úložiště.

### <a name="chart-push-event-payload"></a>Datová část nabízených událostí grafu

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla spuštěna událost webhooku.|
|`action`|Řetězec|Akce, která spustila událost webhooku.|
|[Cíl](#helm_target)|Komplexní typ|Cíl události, která spustila událost webhooku.|

### <a name="target"></a><a name="helm_target"></a>Cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|Řetězec|Digest obsahu, jak je definováno v registru V2 HTTP API specifikace.|
|`repository`|Řetězec|Název úložiště.|
|`tag`|Řetězec|Název značky grafu.|
|`name`|Řetězec|Název grafu.|
|`version`|Řetězec|Verze grafu.|

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

Příklad [příkazu Azure CLI,](/cli/azure/acr) který aktivuje webhook **u události chart_push:**

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Odstranit událost

Webhook se spustil při odstranění úložiště obrázků nebo manifestu. Při odstranění značky se neaktivuje.

### <a name="delete-event-payload"></a>Odstranit datová část události

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla spuštěna událost webhooku.|
|`action`|Řetězec|Akce, která spustila událost webhooku.|
|[Cíl](#delete_target)|Komplexní typ|Cíl události, která spustila událost webhooku.|
|[Požadavek](#delete_request)|Komplexní typ|Požadavek, který vygeneroval událost webhooku.|

### <a name="target"></a><a name="delete_target"></a>Cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`digest`|Řetězec|Digest obsahu, jak je definováno v registru V2 HTTP API specifikace.|
|`repository`|Řetězec|Název úložiště.|

### <a name="request"></a><a name="delete_request"></a>Požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, který inicioval událost.|
|`host`|Řetězec|Externě přístupný název hostitele instance registru, jak je určeno hlavičkou hostitele HTTP na příchozí požadavky.|
|`method`|Řetězec|Metoda požadavku, která vygenerovala událost.|
|`useragent`|Řetězec|Hlavička uživatelského agenta požadavku.|

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

Příklad příkazů [Azure CLI,](/cli/azure/acr) které aktivují **odstranění** webového háku události:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Událost odstranění grafu

Webhook se aktivuje při odstranění grafu helmu nebo úložiště. 

### <a name="chart-delete-event-payload"></a>Graf odstranit datová část událostí

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla spuštěna událost webhooku.|
|`action`|Řetězec|Akce, která spustila událost webhooku.|
|[Cíl](#chart_delete_target)|Komplexní typ|Cíl události, která spustila událost webhooku.|

### <a name="target"></a><a name="chart_delete_target"></a>Cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|Řetězec|Digest obsahu, jak je definováno v registru V2 HTTP API specifikace.|
|`repository`|Řetězec|Název úložiště.|
|`tag`|Řetězec|Název značky grafu.|
|`name`|Řetězec|Název grafu.|
|`version`|Řetězec|Verze grafu.|

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

Příklad [příkazu Azure CLI,](/cli/azure/acr) který aktivuje webhooku chart_delete událost: **chart_delete**

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Další kroky

[Použití webových háků registru kontejnerů Azure](container-registry-webhook.md)