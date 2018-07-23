---
title: Schéma událostí služby Azure Event Grid Container Registry
description: Popisuje vlastnosti, které jsou k dispozici pro kontejner Reigstry událostí pomocí služby Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/20/2018
ms.author: tomfitz
ms.openlocfilehash: 9ed918a7402abcbe79e302421f3b2ac725857464
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188782"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schéma událostí Azure Event Grid pro registr kontejneru

Tento článek obsahuje vlastnosti a schéma pro události Container Registry. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Typy událostí k dispozici

Úložiště objektů BLOB generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Vyvoláno, když se vloží bitovou kopii. |
| Microsoft.ContainerRegistry.ImageDeleted | Vyvoláno, když se odstraní image. |

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

## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | ID události. |
| časové razítko | řetězec | Čas, kdy došlo k události. |
| akce | řetězec | Akce, která zahrnuje zadané události. |
| cíl | objekt | Cíl události. |
| požadavek | objekt | Požadavek, který událost vyvolal. |
| objekt actor | objekt | Agent, který spustil danou událost. Pro většinu situací tato hodnota může být z autorizační kontext požadavku. |
| source | objekt | Uzlu registru, který událost vyvolal. Vložit jinak, zatímco objekt actor zahájí události, zdroj ho generuje. |

Cílový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| mediaType | řetězec | Typ MIME odkazovaného objektu. |
| velikost | integer | Počet bajtů obsahu. Stejné jako délku pole. |
| ověřování algoritmem Digest | řetězec | Přehled obsahu, jak je definováno ve specifikaci protokolu HTTP rozhraní API V2 registru. |
| Délka | integer | Počet bajtů obsahu. Stejná jako velikost pole. |
| Úložiště | řetězec | Název úložiště. |
| url | řetězec | Přímá adresa URL obsahu. |
| značka | řetězec | Název značky. |

Objekt žádosti má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | ID požadavku, který spustil danou událost. |
| addr | řetězec | IP nebo název hostitele a pravděpodobně port připojení klienta, který spustil danou událost. Tato hodnota je RemoteAddr z požadavku standardní http. |
| hostitel | řetězec | Zvenku přístupný název hostitele instance registru, jak je uvedeno v http hlavičce hostitele na příchozí požadavky. |
| method | řetězec | Metoda žádosti, které vygenerovalo událost. |
| userAgent | řetězec | Hlavičky uživatelského agenta žádosti. |

Objekt actor má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| jméno | řetězec | Předmět nebo uživatelské jméno přidružené k kontext požadavku, který událost vyvolal. |

Zdrojový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| addr | řetězec | IP adresa nebo název hostitele a port uzlu registru, který událost vyvolal. Obecně platí tato hodnota se vyřeší podle operačního systému. Hostname() spolu s běžící portu. |
| instanceID | řetězec | Spuštěné instance aplikace. Změny po každém restartování. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
