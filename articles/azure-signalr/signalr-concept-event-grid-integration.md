---
title: Reakce na události služby signalizace Azure
description: K přihlášení k odběru událostí služby signalizace Azure použijte Azure Event Grid. V těchto událostech můžou aktivovat jiné služby pro příjem dat.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74158189"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reakce na události služby Azure SignalR Service

Události služby signalizace Azure umožňují aplikacím reagovat na připojení klientů připojená nebo odpojená pomocí moderních architektur bez serveru. Je to bez nutnosti složitosti složitého kódu nebo nákladných a neefektivních služeb cyklického dotazování.  Místo toho se události odesílají prostřednictvím [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) předplatitelům, jako je [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce i vlastní naslouchací proces http, a platíte jenom za to, co využijete.

Události služby signalizace Azure se spolehlivě odesílají službě Event Grid, která poskytuje vašim aplikacím spolehlivé služby doručování prostřednictvím zásad opakovaného pokusů a doručování s nedoručenými zprávami. Další informace najdete v tématu [Event Grid doručování zpráv a zkuste to znovu](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Model Event Grid](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stav bez serveru
Události služby Azure Signal se aktivují jenom v případě, že jsou připojení klientů ve stavu bez serveru. Obecně řečeno, pokud klient nesměruje na server centrálního serveru, přejde do stavu bez serveru. Klasický režim funguje jenom v případě, že se rozbočovač, ke kterému se připojí klient připojení, nemá hub Server. Nedoporučuje se ale používat režim bez serveru, aby nedocházelo k nějakým potížím. Další informace o režimu služby najdete v tématu [Jak zvolit režim služby](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostupné události služby signalizace Azure
Event Grid používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Odběry událostí služby signalizace Azure podporují dva typy událostí:  

|Název události|Popis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Je aktivována, když je připojení klienta připojeno.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Je aktivována, když je připojení klienta odpojeno.|

## <a name="event-schema"></a>Schéma událostí
Události služby signalizace Azure obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost služby signalizace Azure s vlastností eventType začínající na "Microsoft. SignalRService". Další informace o použití vlastností události Event Grid najdete v části [Event Grid schéma událostí](../event-grid/event-schema.md).  

Tady je příklad události připojené k připojení klienta:
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

Další informace najdete v tématu [schéma událostí služby signalizace](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a poskytněte události služby Azure Signal Service a vyzkoušejte:

> [!div class="nextstepaction"]
> [Vyzkoušejte si ukázku Event Grid integrací se službou Azure Signal Service](./signalr-howto-event-grid-integration.md) 
>  [O Event Grid](../event-grid/overview.md)
