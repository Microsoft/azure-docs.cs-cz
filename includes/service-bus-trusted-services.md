---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510760"
---
## <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu
Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** , udělí se těmto službám přístup k vašim prostředkům Service Bus.

| Důvěryhodná služba | Podporované scénáře použití | 
| --------------- | ------------------------- | 
| Azure Event Grid | Povoluje Azure Event Grid odesílat události do front nebo témat v oboru názvů Service Bus. Je také nutné provést následující kroky: <ul><li>Povolit identitu přiřazenou systémem pro téma nebo doménu</li><li>Přidejte identitu do role Azure Service Bus data Sender v oboru názvů Service Bus</li><li>Pak nakonfigurujte odběr událostí, který používá Service Bus frontu nebo téma jako koncový bod pro použití identity přiřazené systémem.</li></ul> <p>Další informace najdete v tématu [doručování událostí se spravovanou identitou](../articles/event-grid/managed-service-identity.md) .</p>|
| Azure API Management | <p>Služba API Management umožňuje odesílat zprávy do fronty nebo tématu Service Bus v oboru názvů Service Bus.</p><ul><li>Můžete aktivovat vlastní pracovní postupy odesláním zpráv do fronty nebo tématu Service Bus, když se rozhraní API vyvolá pomocí [zásady odeslání požadavku](../articles/api-management/api-management-sample-send-request.md).</li><li>Můžete také považovat Service Bus frontu nebo téma jako back-end v rozhraní API. Ukázkovou zásadu najdete v tématu [ověření pomocí spravované identity pro přístup k Service Bus frontě nebo tématu](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). Je také nutné provést následující kroky:<ol><li>Povolte identitu přiřazenou systémem na instanci API Management. Pokyny najdete v tématu [použití spravovaných identit v Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Přidejte identitu do role **Azure Service Bus data Sender** v oboru názvů Service Bus</li></ol></li></ul> | 