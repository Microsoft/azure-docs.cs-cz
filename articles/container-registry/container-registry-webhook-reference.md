---
title: Referenční dokumentace schématu webhook registru kontejneru Azure
description: Webhook požadavku JSON datové části referenční informace pro Azure Container Registry.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: 87fe978416c29b50abeef0e0a6624d7440dd87ef
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096889"
---
# <a name="azure-container-registry-webhook-reference"></a>Referenční informace webhook služby Azure Container Registry

Je možné [konfigurace webhooků](container-registry-webhook.md) pro svůj registr kontejneru, které generují události, kdy byly provedeny některé akce před ním. Například můžete povolit webhooky, které se spouštějí na image kontejneru `push` a `delete` operace. Když webhooku se aktivuje, Azure Container Registry vydá požadavek HTTP či HTTPS obsahující informace o události do koncového bodu, který zadáte. Váš koncový bod můžete zpracovat webhook a příslušně na ně reagovat.

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

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|Řetězec|Akce, který spustil danou událost webhooku.|
|[Cíl](#target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#request)|Komplexní typ|Žádosti, které vygenerovalo událost webhooku.|

### <a name="target"></a>cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`size`|Datový typ Int32|Počet bajtů obsahu. Stejné jako délku pole.|
|`digest`|Řetězec|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`length`|Datový typ Int32|Počet bajtů obsahu. Stejná jako velikost pole.|
|`repository`|Řetězec|Název úložiště.|
|`tag`|Řetězec|Název obrázku značky.|

### <a name="request"></a>požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, který spustil danou událost.|
|`host`|Řetězec|Zvenku přístupný název hostitele instance registru, jak je uvedeno v HTTP hlavičce hostitele na příchozí požadavky.|
|`method`|Řetězec|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|Řetězec|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-push-event"></a>Příklad datové části: nabízených oznámení událostí

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Příklad [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) příkaz, který aktivuje **nabízených** události webhooku:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Odstranit událost

Webhook se aktivuje, když je odstraněn úložiště nebo manifest. Není aktivováno, když se odstraní značky.

### <a name="delete-event-payload"></a>Odstranit datovou část události

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|Řetězec|Akce, který spustil danou událost webhooku.|
|[Cíl](#delete_target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#delete_request)|Komplexní typ|Žádosti, které vygenerovalo událost webhooku.|

### <a name="delete_target"></a> Cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`digest`|Řetězec|Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru.|
|`repository`|Řetězec|Název úložiště.|

### <a name="delete_request"></a> Požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, který spustil danou událost.|
|`host`|Řetězec|Zvenku přístupný název hostitele instance registru, jak je uvedeno v HTTP hlavičce hostitele na příchozí požadavky.|
|`method`|Řetězec|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|Řetězec|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-delete-event"></a>Příklad datové části: odstranit událost

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Příklad [rozhraní příkazového řádku Azure](/cli/azure/acr) příkazy, které spouštějí **odstranit** události webhooku:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Další postup

[Použití webhooků v Azure Container Registry](container-registry-webhook.md)