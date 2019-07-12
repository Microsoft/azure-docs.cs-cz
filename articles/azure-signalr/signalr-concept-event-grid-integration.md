---
title: Reakce na události služby Azure SignalR
description: Pomocí Azure Event Grid pro přihlášení k odběru událostí služby Azure SignalR.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789186"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reakce na události služby Azure SignalR

Události Azure SignalR služby umožňují aplikacím reagovat na připojení klientů připojení nebo odpojení pomocí moderní architektury bez serveru. Dělá to bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby.  Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele, jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní naslouchací proces protokolu http vlastní a pouze Plaťte za to co používáte.

Azure události služby SignalR spolehlivě odesílají do služby Event Grid, která nabízí spolehlivé doručování služeb do aplikací prostřednictvím bohaté opakování zásady a doručování onta nedoručených zpráv. Další informace najdete v tématu [doručování zpráv služby Event Grid a zkuste to znovu](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid modelu](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Bez serveru stavu
Azure SignalR služby události jsou aktivní, pouze při připojení klienta jsou ve stavu bez serveru. Obecně řečeno pokud klient nebude směrovat do serveru pro rozbočovač, přejde do stavu bez serveru. Klasický režim pracovní jenom v případě, že centrum, která klientská připojení připojit, nemá server rozbočovače. Režim bez serveru doporučujeme však, aby problém. Další podrobnosti o režimu služby najdete v tématu [jak zvolit režim služby](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostupná událostem služby Azure SignalR
Pomocí služby Event grid [odběry událostí](../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele. Odběry událostí Azure SignalR Service podporuje dva typy událostí:  

|Název události|Popis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Vyvolá se při připojení klienta.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Vyvolá se při odpojení připojení klienta.|

## <a name="event-schema"></a>Schéma událostí
Azure události služby SignalR obsahovat všechny informace, které je potřeba reagovat na změny ve vašich datech. Můžete identifikovat služby Azure SignalR spustí událost s vlastností eventType s "Microsoft.SignalRService". Další informace o použití vlastností událostí služby Event Grid je popsána v [schéma událostí služby Event Grid](../event-grid/event-schema.md).  

Tady je příklad připojené události připojení klienta:
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

Další informace najdete v tématu [schématu událostí služby SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Další postup

Další informace o službě Event Grid a vyzkoušejte služby Azure SignalR události:

> [!div class="nextstepaction"]
> [Zkuste integraci služby Event Grid ukázka pomocí služby Azure SignalR](./signalr-howto-event-grid-integration.md)
> [služby Event Grid](../event-grid/overview.md)
