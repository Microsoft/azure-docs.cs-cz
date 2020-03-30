---
title: Reakce na události služby Azure SignalR
description: K odběru událostí služby Azure SignalR service použijte Azure Event Grid. Další následné služby mohou být spuštěny těmito událostmi.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158189"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reakce na události služby Azure SignalR Service

Události služby Azure SignalR umožňují aplikacím reagovat na připojení klientů připojená nebo odpojená pomocí moderních architektur bez serveru. Činí tak bez nutnosti složitého kódu nebo nákladné a neefektivní volební služby.  Místo toho se události přessazují přes [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) předplatitelům, jako jsou Azure [Functions](https://azure.microsoft.com/services/functions/), Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce na vlastní http listener, a platíte jenom za to, co používáte.

Události služby Azure SignalR Service se spolehlivě odesílají do služby Event Grid, která poskytuje spolehlivé doručovací služby vašim aplikacím prostřednictvím bohatých zásad opakování a doručování nedoručených dopisů. Další informace najdete v [tématu Doručování zpráv a opakování zprávy v mřížce událostí](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Model mřížky událostí](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stav bez serveru
Události služby Azure SignalR service jsou aktivní jenom v případě, že připojení klientů jsou ve stavu bez serveru. Obecně řečeno, pokud klient není směrování na centrální server, přejde do stavu bez serveru. Klasický režim funguje pouze v případě, že rozbočovač, ke kterému se klientská připojení připojují, nemá centrální server. Režim bez serveru se však doporučuje, aby se zabránilo nějakému problému. Další informace o servisním režimu najdete v tématu [Jak zvolit servisní režim](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostupné události služby Azure SignalR
Mřížka událostí používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Předplatná událostí služby Azure SignalR podporují dva typy událostí:  

|Název události|Popis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Je aktivována při připojení klienta.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Je aktivována při odpojení připojení klienta.|

## <a name="event-schema"></a>Schéma událostí
Události služby Azure SignalR service obsahují všechny informace, které potřebujete k reakci na změny v datech. Můžete identifikovat událost služby Azure SignalR s vlastností eventType začínající "Microsoft.SignalRService". Další informace o použití vlastností události Event Grid jsou popsány ve [schématu událostí Event Grid](../event-grid/event-schema.md).  

Zde je příklad události připojené k připojení klienta:
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

Další informace naleznete [v tématu Schéma událostí služby SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a vyzkoušejte události služby Azure SignalR:

> [!div class="nextstepaction"]
> [Vyzkoušejte ukázkovou integraci gridu událostí se službou](./signalr-howto-event-grid-integration.md)
> Azure SignalR[o event gridu](../event-grid/overview.md)
