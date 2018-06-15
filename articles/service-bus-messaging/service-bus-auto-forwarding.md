---
title: Auto předávání entity pro zasílání zpráv Azure Service Bus | Microsoft Docs
description: Jak řetězu fronty Service Bus nebo předplatné do jiné fronty nebo téma.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: be23d919b0c96d6c9b96ee328d1b18ad978a9dcc
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
ms.locfileid: "29558088"
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Řetězení entit služby Service Bus s automatické přesměrování

Service Bus *automatické přesměrování* funkce vám umožní řetězit k fronty nebo předplatné do jiné fronty nebo téma, které je součástí stejného oboru názvů. Pokud je povoleno automatické přesměrování, Service Bus automaticky odebere zprávy, které jsou umístěny v první fronty nebo předplatné (zdroj) a umístí je do druhé fronta nebo téma (cíl). Všimněte si, že je stále možné odeslat zprávu do cílové entity přímo. Navíc není možné zřetězit dílčí fronta, jako je například fronty nedoručených zpráv, do jiné fronty nebo téma.

## <a name="using-auto-forwarding"></a>Pomocí automatické přesměrování

Můžete povolit automatické přesměrování nastavením [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] nebo [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] vlastnosti [QueueDescription] [ QueueDescription] nebo [SubscriptionDescription] [ SubscriptionDescription] objekty pro zdroj, jako v Následující příklad:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Cílová entita musí existovat v době, kdy se vytvoří zdrojové entity. Pokud Cílová entita neexistuje, vrátí se Service Bus výjimku, pokud se zobrazí výzva k vytvoření zdrojové entitě.

Automatické přesměrování můžete škálovat jednotlivé tématu. Omezení služby Service Bus [počet předplatných na dané téma](service-bus-quotas.md) na 2 000. Další odběry zvládne vytvořením témata druhé úrovně. I když Service Bus omezení nejsou vázány na počet odběrů, přidání druhou úroveň témata může zvýšit celkovou propustnost téma.

![Scénáře automatické přesměrování][0]

Můžete taky automaticky předávání oddělit odesílatelé zpráv z příjemců. Představte si třeba ERP systém, který se skládá ze tří modulů: pořadí zpracování, Správa inventáře a řízení vztahů se zákazníky. Každý z těchto modulů generuje zprávy, které jsou zařazených do fronty do příslušné téma. Alice a Bob jsou obchodní zástupce, kteří se chtějí všechny zprávy, které se vztahují ke svým zákazníkům. Aby se tyto zprávy Alice a Bob vytvořit osobní fronty a odběru na každém ERP témat, která automaticky předávat všechny zprávy do jejich fronty.

![Scénáře automatické přesměrování][1]

Pokud Alice přejde na dovolenou, svůj osobní fronty, nikoli tématu ERP, zaplní. V tomto scénáři protože obchodním zástupcem neobdržel všechny zprávy žádné témat ERP někdy dosáhnout kvóty.

## <a name="auto-forwarding-considerations"></a>Důležité informace o automatické přesměrování

Pokud Cílová entita shromažďuje příliš mnoho zpráv a překročí kvótu nebo Cílová entita je zakázaná, přidá zdrojové entitě zprávy na jeho [frontu nedoručených zpráv](service-bus-dead-letter-queues.md) dokud není místa v cílovém (nebo entita je znovu povolit). Tyto zprávy se nadále za provozu do fronty nedoručených zpráv, takže se musí explicitně zobrazí a jejich zpracování z fronty nedoručených zpráv.

Pokud řetězení společně jednotlivých témata získat složené téma se mnoho odběrů, doporučuje se, že máte středně velkým počtem odběry tématu první úrovně a mnoho odběrů na témata druhé úrovně. Například téma první úrovně se 20 odběry, každý z nich zřetězené téma druhé úrovně se 200 odběry, umožňuje vyšší propustnost než první úrovně téma se 200 odběry, každý zřetězené do tématu druhé úrovně s 20 odběry.

Service Bus bills jednu operaci pro každý přesměrovaná zpráva. Například odesílání zprávy do tématu s 20 odběry, každý z nich nakonfigurovat tak, aby automaticky předání zprávy do jiné fronty nebo téma, se fakturuje jako 21 operace Pokud všechny odběry první úrovně obdrží kopii zprávy.

Chcete-li vytvořit odběr, který je zřetězené do jiné fronty nebo téma, musí mít Tvůrce odběru **spravovat** oprávnění na zdrojovém i cílovém entity. Odesílání zpráv do tématu zdroj vyžaduje pouze **odeslat** oprávnění k tématu zdroje.

## <a name="next-steps"></a>Další postup

Podrobné informace o automatické přesměrování najdete v těchto tématech:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Další informace o vylepšení výkonu služby Service Bus, najdete v části 

* [Osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](service-bus-performance-improvements.md)
* [Segmentované entity zasílání zpráv][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
