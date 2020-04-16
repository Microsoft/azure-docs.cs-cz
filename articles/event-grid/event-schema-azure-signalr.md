---
title: Azure SingnalR jako zdroj mřížky událostí
description: Popisuje vlastnosti, které jsou k dispozici pro události Azure SignalR s Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393399"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schéma událostí Azure Event Grid pro službu SignalR

Tento článek obsahuje vlastnosti a schéma událostí služby SignalR.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md). Poskytuje také seznam rychlých spuštění a kurzů pro použití Azure SignalR jako zdroj událostí.

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

### <a name="available-event-types"></a>Dostupné typy událostí

Služba SignalR vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Připojení Microsoft.SignalRService.ClientConnection | Je aktivována při připojení klienta. |
| Microsoft.SignalRService.ClientConnectionOdpojeno | Je aktivována při odpojení připojení klienta. |

### <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma události připojeného k připojení klienta: 

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

Schéma pro událost odpojeného připojení klienta je podobné: 

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

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí signalr service. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| časové razítko | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| název hubu | řetězec | Rozbočovač, ke kterému patří připojení klienta. |
| connectionId | řetězec | Jedinečný identifikátor pro připojení klienta. |
| userId | řetězec | Identifikátor uživatele definovaný v deklaraci. |
| Errormessage | řetězec | Chyba, která způsobuje odpojení připojení. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis | Popis |
|---------|---------|
| [Reakce na události služby Azure SignalR pomocí mřížky událostí](../azure-signalr/signalr-concept-event-grid-integration.md) | Přehled integrace služby Azure SignalR s gridem událostí. |
| [Jak odeslat události služby Azure SignalR do mřížky událostí](../azure-signalr/signalr-howto-event-grid-integration.md) | Ukazuje, jak odeslat události služby Azure SignalR do aplikace prostřednictvím služby Event Grid. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
