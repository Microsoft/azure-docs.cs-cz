---
title: Referenční dokumentace schématu webhook registru kontejneru Azure
description: Webhook požadavku JSON datové části referenční informace pro Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099895"
---
# <a name="azure-container-registry-webhook-reference"></a>Referenční informace webhook služby Azure Container Registry

Je možné [konfigurace webhooků](container-registry-webhook.md) pro svůj registr kontejneru, které generují události, kdy byly provedeny některé akce před ním. Například povolte webhooky, které se zobrazí, když image kontejneru nebo grafu helmu je nahrány do registru, nebo odstranit. Když webhooku se aktivuje, Azure Container Registry vydá požadavek HTTP či HTTPS obsahující informace o události do koncového bodu, který zadáte. Váš koncový bod můžete zpracovat webhook a příslušně na ně reagovat.

Následující části popisují schéma webhooku požadavkům vygenerovaným rozhraním podporované události. Části události obsahují datové části schéma pro typ události, datovou část požadavku příklad a jeden nebo více příklady příkazů, které by aktivují webhook.

Informace o konfiguraci webhooků pro službu Azure container registry najdete v tématu [pomocí Azure Container Registry webhooky](container-registry-webhook.md).

## <a name="webhook-requests"></a>Požadavky Webhooku

### <a name="http-request"></a>Požadavek protokolu HTTP

Díky protokolu HTTP aktivované webhookem `POST` požadavek na koncový bod adresy URL zadané při konfiguraci webhooku.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Zahrnují požadavky Webhooku `Content-Type` z `application/json` v případě špatných `Content-Type` vlastní hlavičky pro vaše webhooku.

Žádné hlavičky jsou přidány do požadavku nad rámec těchto vlastní hlavičky, které jste určili pro webhook.

## <a name="push-event"></a>Nabízených oznámení událostí

Webhook se aktivuje, když je nasdílení imagí kontejneru do úložiště.

### <a name="push-event-payload"></a>Datová část události nabízených oznámení

|Element|Type|Popis|
|-------------|----------|-----------|
|`id`|String|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|String|Akce, který spustil danou událost webhooku.|
|[target](#target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#request)|Komplexní typ|Žádosti, které vygenerovalo událost webhooku.|

### <a name="target"></a>Cíl

|Element|Type|Popis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu. Stejné jako délku pole.|
|`digest`|String|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`length`|Int32|Počet bajtů obsahu. Stejná jako velikost pole.|
|`repository`|String|Název úložiště.|
|`tag`|String|Název obrázku značky.|

### <a name="request"></a>Požadavek

|Element|Type|Popis|
|------------------|----------|-----------|
|`id`|String|ID požadavku, který spustil danou událost.|
|`host`|String|Zvenku přístupný název hostitele instance registru, jak je uvedeno v HTTP hlavičce hostitele na příchozí požadavky.|
|`method`|String|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|String|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-image-push-event"></a>Datové části Příklad: image nabízených oznámení událostí

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

Příklad [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) příkaz, který aktivuje image **nabízených** události webhooku:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Graf nabízených oznámení událostí

Webhook se aktivuje, když grafu helmu je vloženo do úložiště.

### <a name="chart-push-event-payload"></a>Datová část události nabízených grafu

|Element|Type|Popis|
|-------------|----------|-----------|
|`id`|String|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|String|Akce, který spustil danou událost webhooku.|
|[target](#helm_target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|

### <a name="helm_target"></a>Cíl

|Element|Type|Popis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|String|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`repository`|String|Název úložiště.|
|`tag`|String|Název značky grafu.|
|`name`|String|Název grafu.|
|`version`|String|Verze grafu.|

### <a name="payload-example-chart-push-event"></a>Příklad datové části: grafu nabízených oznámení událostí

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

Příklad [rozhraní příkazového řádku Azure](/cli/azure/acr) příkaz, který aktivuje **chart_push** události webhooku:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Odstranit událost

Webhook se aktivuje, když je úložiště image nebo manifest se odstraní. Není aktivováno, když se odstraní značky.

### <a name="delete-event-payload"></a>Odstranit datovou část události

|Element|Type|Popis|
|-------------|----------|-----------|
|`id`|String|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|String|Akce, který spustil danou událost webhooku.|
|[target](#delete_target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#delete_request)|Komplexní typ|Žádosti, které vygenerovalo událost webhooku.|

### <a name="delete_target"></a> Cíl

|Element|Type|Popis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME odkazovaného objektu.|
|`digest`|String|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`repository`|String|Název úložiště.|

### <a name="delete_request"></a> Požadavek

|Element|Type|Popis|
|------------------|----------|-----------|
|`id`|String|ID požadavku, který spustil danou událost.|
|`host`|String|Zvenku přístupný název hostitele instance registru, jak je uvedeno v HTTP hlavičce hostitele na příchozí požadavky.|
|`method`|String|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|String|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-image-delete-event"></a>Příklad datové části: událostí odstranit bitové kopie

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

Příklad [rozhraní příkazového řádku Azure](/cli/azure/acr) příkazy, které spouštějí **odstranit** události webhooku:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Událost odstranění grafu

Webhook se aktivuje, když je odstraněn grafu helmu nebo úložiště. 

### <a name="chart-delete-event-payload"></a>Datová část události odstranit graf

|Element|Type|Popis|
|-------------|----------|-----------|
|`id`|String|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|String|Akce, který spustil danou událost webhooku.|
|[target](#chart_delete_target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|

### <a name="chart_delete_target"></a> Cíl

|Element|Type|Popis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu.|
|`digest`|String|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`repository`|String|Název úložiště.|
|`tag`|String|Název značky grafu.|
|`name`|String|Název grafu.|
|`version`|String|Verze grafu.|

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

Příklad [rozhraní příkazového řádku Azure](/cli/azure/acr) příkaz, který aktivuje **chart_delete** události webhooku:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Další postup

[Použití webhooků v Azure Container Registry](container-registry-webhook.md)