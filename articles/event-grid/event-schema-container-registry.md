---
title: Registr kontejnerů Azure jako zdroj mřížky událostí
description: Popisuje vlastnosti, které jsou k dispozici pro události registru kontejneru s Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7e33feb04edf42f1e2a32b9b8c8e2fd214692f31
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393367"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Azure Container Registry jako zdroj mřížky událostí

Tento článek obsahuje vlastnosti a schéma pro události registru kontejnerů.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

### <a name="available-event-types"></a>Dostupné typy událostí

Azure Container Registry vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Je aktivována při stisknutí obrázku. |
| Microsoft.ContainerRegistry.ImageDeleted | Je aktivována při odstranění obrázku. |
| Microsoft.ContainerRegistry.ChartPushed | Je aktivována při stisknutí grafu Helm. |
| Microsoft.ContainerRegistry.ChartDeleted | Je aktivována při odstranění grafu Helm. |

### <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma události posunuté obrázkem: 

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

Schéma události odstraněného obrázku je podobné:

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

Schéma události push grafu je podobné schématu pro událost s obrázkovou posunutou, ale neobsahuje objekt požadavku:

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

Schéma pro událost odstraněné grafem je podobné schématu pro odstraněnou událost s obrázkem, ale neobsahuje objekt požadavku:

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

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí úložiště objektů blob. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | ID události. |
| časové razítko | řetězec | Čas, kdy k události došlo. |
| action | řetězec | Akce, která zahrnuje zapředpokladu událost. |
| Cíl | objekt | Cíl události. |
| Požadavek | objekt | Požadavek, který vygeneroval událost. |

Cílový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Mediatype | řetězec | Typ MIME odkazovaného objektu. |
| velikost | celé číslo | Počet bajtů obsahu. Stejné jako pole Length. |
| digest | řetězec | Digest obsahu, jak je definováno v registru V2 HTTP API specifikace. |
| length | celé číslo | Počet bajtů obsahu. Stejné jako pole Velikost. |
| úložiště | řetězec | Název úložiště. |
| značka | řetězec | Název značky. |
| jméno | řetězec | Název grafu. |
| version | řetězec | Verze grafu. |

Objekt požadavku má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | ID požadavku, který inicioval událost. |
| Addr | řetězec | Název IP nebo hostitele a případně port připojení klienta, který událost inicioval. Tato hodnota je RemoteAddr ze standardního požadavku http. |
| host | řetězec | Externě přístupný název hostitele instance registru, jak je určeno hlavičkou hostitele http na příchozí požadavky. |
| method | řetězec | Metoda požadavku, která vygenerovala událost. |
| Useragent | řetězec | Hlavička uživatelského agenta požadavku. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis |Popis  |
|---------|---------|
| [Úvodní příručka: Odeslání událostí registru kontejneru](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k odesílání událostí registru kontejnerů. |


## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
