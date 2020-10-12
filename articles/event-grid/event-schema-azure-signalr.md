---
title: Azure Signal jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Azure Signal s Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106647"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid schéma událostí pro službu Signal

Tento článek poskytuje vlastnosti a schéma pro události služby signalizace.Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Nabízí také seznam rychlých startů a kurzů pro použití služby Azure Signal jako zdroje událostí.

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Služba signalizace emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Vyvolá se, když se připojí připojení klienta. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Vyvolá se v případě, že bylo připojení klienta odpojeno. |

### <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma připojené události připojení klienta: 

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

Schéma pro událost odpojení klientského připojení je podobné: 

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

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | object | Data události služby signalizace. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| časové razítko | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| hubName | řetězec | Rozbočovač, ke kterému patří připojení klienta. |
| connectionId | řetězec | Jedinečný identifikátor pro připojení klienta. |
| userId | řetězec | Identifikátor uživatele definovaný v deklaraci identity. |
| Chybová | řetězec | Chyba, která způsobí odpojení připojení. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis | Popis |
|---------|---------|
| [Reakce na události služby signalizace Azure pomocí Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Přehled integrace služby Azure Signaler pomocí Event Grid. |
| [Odeslání událostí služby signalizace Azure do Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Ukazuje, jak odesílat události služby signalizace Azure do aplikace prostřednictvím Event Grid. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
