---
title: Schéma událostí služby Azure Event Grid Container Registry
description: Popisuje vlastnosti, které jsou k dispozici pro události Container Registry pomocí Azure Event gridu
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084323"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schéma událostí Azure Event Grid pro registr kontejneru

Tento článek obsahuje vlastnosti a schéma pro události Container Registry. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Typy událostí k dispozici

Služba Azure Container Registry generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Vyvoláno, když se vloží bitovou kopii. |
| Microsoft.ContainerRegistry.ImageDeleted | Vyvoláno, když se odstraní image. |
| Microsoft.ContainerRegistry.ChartPushed | Vyvoláno, když se vloží Digram helmu. |
| Microsoft.ContainerRegistry.ChartDeleted | Vyvolá se při odstranění Digram helmu. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu bitovou kopii vložena událost: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schéma událostí odstranit bitové kopie je podobné:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schéma grafu vložena událost je podobný schéma vytvořenými bitovými kopiemi vložené události, ale neobsahuje objekt žádosti:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schéma grafu odstranit události je podobný schéma pro vytvořenými bitovými kopiemi pro odstraněnou událost, ale neobsahuje objekt žádosti:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | string | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| předmět | string | Vydavatel definované cesta předmět události. |
| eventType | string | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | string | Vygenerování události podle času UTC poskytovatele. |
| id | string | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | string | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | string | ID události. |
| časové razítko | string | Čas, kdy došlo k události. |
| action | string | Akce, která zahrnuje zadané události. |
| cíl | objekt | Cíl události. |
| žádost | objekt | Požadavek, který událost vyvolal. |

Cílový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| mediaType | string | Typ MIME odkazovaného objektu. |
| velikost | integer | Počet bajtů obsahu. Stejné jako délku pole. |
| ověřování algoritmem Digest | string | Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru. |
| Délka | integer | Počet bajtů obsahu. Stejná jako velikost pole. |
| úložiště | string | Název úložiště. |
| značka | string | Název značky. |
| jméno | string | Název grafu. |
| version | string | Verze grafu. |

Objekt žádosti má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | string | ID požadavku, který spustil danou událost. |
| addr | string | IP nebo název hostitele a pravděpodobně port připojení klienta, který spustil danou událost. Tato hodnota je RemoteAddr z požadavku standardní http. |
| hostitel | string | Zvenku přístupný název hostitele instance registru, jak je uvedeno v http hlavičce hostitele na příchozí požadavky. |
| method | string | Metoda žádosti, které vygenerovalo událost. |
| userAgent | string | Hlavičky uživatelského agenta žádosti. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
