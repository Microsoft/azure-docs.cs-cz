---
author: baanders
description: zahrnout soubor pro prostředky, které jsou potřeba k vytvoření koncových bodů digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054499"
---
### <a name="prerequisite-create-endpoint-resources"></a>Předpoklad: vytvoření prostředků koncového bodu

Aby bylo možné propojit koncový bod s digitálními podsítěmi Azure, musí již existovat téma Event gridu, centrum událostí nebo Service Bus téma, které používáte pro koncový bod.

Pomocí následujícího grafu zjistíte, jaké prostředky je potřeba nastavit ještě před vytvořením koncového bodu.

| Typ koncového bodu | Požadované prostředky (propojené s pokyny k vytvoření) |
| --- | --- |
| Event Grid koncový bod | [téma Event gridu](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs koncový bod | [&nbsp; &nbsp; Obor názvů centra událostí](../articles/event-hubs/event-hubs-create.md)<br/><br/>[centrum událostí](../articles/event-hubs/event-hubs-create.md)<br/><br/>Volitelné [autorizační pravidlo](../articles/event-hubs/authorize-access-shared-access-signature.md) pro ověřování na základě klíčů | 
| Service Bus koncový bod | [Obor názvů Service Bus](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus téma](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Volitelné [autorizační pravidlo](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) pro ověřování na základě klíčů|
