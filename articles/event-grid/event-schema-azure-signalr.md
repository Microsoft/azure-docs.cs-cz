---
title: Schéma událostí služby Azure Event Grid Azure SignalR
description: Popisuje vlastnosti, které jsou k dispozici pro Azure SignalR událostí pomocí služby Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789069"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schéma událostí Azure Event Grid pro služby SignalR

Tento článek obsahuje vlastnosti a schéma pro události služby SignalR. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Typy událostí k dispozici

Služba SignalR generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Vyvolá se při připojení klienta. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Vyvolá se při odpojení připojení klienta. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu klienta připojení připojené události: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schéma pro událost odpojené připojení klienta je podobné: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| topic | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| subject | řetězec | Vydavatel definované cesta předmět události. |
| eventType | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| eventTime | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události služby SignalR. |
| dataVersion | řetězec | Verze schématu datového objektu. Vydavatel Určuje verzi schématu. |
| metadataVersion | řetězec | Verze schématu metadat události. Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| timestamp | řetězec | Vygenerování události podle času UTC poskytovatele. |
| hubName | řetězec | Rozbočovač, který patří připojení klienta. |
| connectionId | řetězec | Jedinečný identifikátor pro připojení klienta. |
| userId | řetězec | Identifikátor uživatele podle deklarací identity. |
| errorMessage | řetězec | Chyba, která způsobí odpojení připojení. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
