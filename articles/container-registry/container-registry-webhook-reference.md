---
title: Referenční dokumentace schématu webhooku registru kontejner Azure
description: Webhooku požadavku JSON datové části odkaz pro registru kontejner Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-webhook-reference"></a>Odkaz na Azure webhooku registru kontejneru

Můžete [konfigurace webhooky](container-registry-webhook.md) pro vaše registru kontejneru, který generovat události při určitých akcí u ní. Například můžete povolit webhooků, které se spouštějí na bitovou kopii kontejneru `push` a `delete` operace. Když se aktivuje webhook, jehož registru kontejner Azure vydá požadavek HTTP či HTTPS obsahující informace o události na koncový bod, který zadáte. Váš koncový bod můžete zpracovat webhooku a odpovídajícím způsobem fungovat.

Následujících oddílech jsou upřesněny schéma žádostí webhooku generovaných událostmi podporované. Části událostí obsahují datové schéma pro typ události, datovou část požadavku příklad a jeden nebo více příklady příkazů, které by aktivovat webhooku.

Informace o konfiguraci webhooky pro vaše registru kontejner Azure najdete v tématu [pomocí registru kontejner Azure webhooky](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhooku požadavky

### <a name="http-request"></a>Požadavek protokolu HTTP

Díky spouštěná webhooku protokolu HTTP `POST` požadavek na koncový bod adresy URL, jste zadali, když jste nakonfigurovali webhooku.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Žádosti o Webhooku zahrnovat `Content-Type` z `application/json` Pokud jste nezadali `Content-Type` vlastní hlavičky vaší webhooku.

Žádná další záhlaví jsou přidány k žádosti nad rámec těchto vlastní hlavičky, kterou jste určili pro webhooku.

## <a name="push-event"></a>Oznámení událostí

Webhooku se aktivuje, když bitovou kopii kontejneru vložena do úložiště.

### <a name="push-event-payload"></a>Datová část události push

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|Řetězec|Akce, která aktivuje událost webhooku.|
|[cíl](#target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#request)|Komplexní typ|Požadavek, který generuje události webhooku.|

### <a name="target"></a>cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`size`|Int32|Počet bajtů obsahu. Stejné jako pole s délkou.|
|`digest`|Řetězec|Hodnotu hash obsahu, podle definice specifikace rozhraní API registru V2 HTTP.|
|`length`|Int32|Počet bajtů obsahu. Stejná jako velikost pole.|
|`repository`|Řetězec|Název úložiště.|
|`tag`|Řetězec|Název obrázku značky.|

### <a name="request"></a>Požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, která iniciovala události.|
|`host`|Řetězec|Externě dostupný název hostitele instance registru, jak je uvedeno v hlavičce HTTP hostitele na příchozí požadavky.|
|`method`|Řetězec|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|Řetězec|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-push-event"></a>Datová část příklad: nabízené událostí

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

Příklad [příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) příkaz, který aktivuje **nabízené** webhooku událostí:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Odstranit událost

Webhooku se aktivuje, když úložiště nebo manifest se odstraní. Neaktivuje při odstranění značku.

### <a name="delete-event-payload"></a>Odstranit datová část události

|Element|Typ|Popis|
|-------------|----------|-----------|
|`id`|Řetězec|ID události webhooku.|
|`timestamp`|DateTime|Čas, kdy byla aktivována událost webhooku.|
|`action`|Řetězec|Akce, která aktivuje událost webhooku.|
|[cíl](#delete_target)|Komplexní typ|Cíl události, která aktivuje událost webhooku.|
|[Požadavek](#delete_request)|Komplexní typ|Požadavek, který generuje události webhooku.|

### <a name="delete_target"></a> cíl

|Element|Typ|Popis|
|------------------|----------|-----------|
|`mediaType`|Řetězec|Typ MIME odkazovaného objektu.|
|`digest`|Řetězec|Hodnotu hash obsahu, podle definice specifikace rozhraní API registru V2 HTTP.|
|`repository`|Řetězec|Název úložiště.|

### <a name="delete_request"></a> Požadavek

|Element|Typ|Popis|
|------------------|----------|-----------|
|`id`|Řetězec|ID požadavku, která iniciovala události.|
|`host`|Řetězec|Externě dostupný název hostitele instance registru, jak je uvedeno v hlavičce HTTP hostitele na příchozí požadavky.|
|`method`|Řetězec|Metoda žádosti, které vygenerovalo událost.|
|`useragent`|Řetězec|Hlavičky uživatelského agenta žádosti.|

### <a name="payload-example-delete-event"></a>Datová část příklad: odstranit událost

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

Příklad [Azure CLI 2.0](/cli/azure/acr) příkazy této aktivační události **odstranit** webhooku událostí:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Další postup

[Pomocí webhooků registru kontejner Azure](container-registry-webhook.md)