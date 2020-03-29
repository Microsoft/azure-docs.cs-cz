---
title: Azure Service Bus rozpoznávání duplicitní zprávy | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak můžete zjistit duplicity ve zprávách Azure Service Bus. Duplicitní zpráva může být ignorována a vynechána.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760364"
---
# <a name="duplicate-detection"></a>Vyhledávání duplicit

Pokud aplikace selže z důvodu závažné chyby ihned po odeslání zprávy a restartování instance aplikace chybně se domnívá, že nedošlo k doručení předchozí zprávy, následné odeslání způsobí, že se v systému zobrazí dvakrát stejná zpráva.

Je také možné, že dojde k chybě na úrovni klienta nebo sítě o chvíli dříve a odeslaná zpráva bude potvrzena do fronty, přičemž potvrzení nebylo klientovi úspěšně vráceno. Tento scénář ponechá klienta na pochybách o výsledku operace odeslání.

Vyhledávání duplicit bere pochybnosti z těchto situací povolením odesílatele znovu odeslat stejnou zprávu a fronty nebo tématu zahodí všechny duplicitní kopie.

Povolení vyhledávání duplicit pomáhá sledovat aplikace řízené *MessageId* všech zpráv odeslaných do fronty nebo tématu během zadaného časového období. Pokud je odeslána nová zpráva s *MessageId,* která byla zaznamenána během časového okna, zpráva je hlášena jako přijata (operace odeslání úspěšné), ale nově odeslaná zpráva je okamžitě ignorována a vynechána. Žádné jiné části zprávy než *MessageId* jsou považovány za.

Řízení aplikace identifikátoru je nezbytné, protože pouze to umožňuje aplikaci svázat *MessageId* s kontextem obchodního procesu, ze kterého lze předvídatelně rekonstruovat, když dojde k selhání.

Pro obchodní proces, ve kterém jsou odesílány více zpráv v průběhu zpracování některých kontextu aplikace *MessageId* může být složený identifikátor kontextu na úrovni aplikace, jako je například číslo nákupní objednávky a předmět zprávy, například **12345.2017/payment**.

*MessageId* může být vždy některé GUID, ale ukotvení identifikátor u obchodního procesu výnosy předvídatelné opakovatelnost, která je žádoucí pro efektivní využití funkce vyhledávání duplicit.

> [!NOTE]
> Pokud je povoleno vyhledávání duplicit a Není nastaveno ID relace nebo klíč oddílu, použije se jako klíč oddílu ID zprávy. Pokud není nastaveno id zprávy, knihovny .NET a AMQP automaticky vygenerují ID zprávy pro zprávu. Další informace naleznete [v tématu Použití klíčů oddílů](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Povolit vyhledávání duplicit

Na portálu je funkce zapnuta během vytváření entit se zaškrtávacím políčkem **Povolit vyhledávání duplicit,** které je ve výchozím nastavení vypnuto. Nastavení pro vytváření nových témat je ekvivalentní.

![][1]

> [!IMPORTANT]
> Po vytvoření fronty nelze povolit nebo zakázat vyhledávání duplicit. To lze provést pouze v době vytváření fronty. 

Programově nastavíte příznak s [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) vlastnost v úplné rozhraní ROZHRANÍ .NET API. S rozhraním API Správce prostředků Azure je hodnota nastavena pomocí [vlastnosti queueProperties.requiresDuplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Historie času vyhledávání duplicit je výchozí 30 sekund pro fronty a témata s maximální hodnotou sedm dní. Toto nastavení můžete změnit v okně vlastností fronty a tématu na webu Azure Portal.

![][2]

Programově můžete nakonfigurovat velikost okna vyhledávání duplicit, během kterého jsou zachovány id zpráv, pomocí [vlastnosti QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) s úplným rozhraním API rozhraní .NET Framework. S rozhraním API Správce prostředků Azure je hodnota nastavena pomocí [vlastnosti queueProperties.duplicateDetectionHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Povolení vyhledávání duplicit a velikost okna přímo ovlivnit fronty (a téma) propustnost, protože všechny zaznamenané id zprávy musí být porovnány s nově odeslané zprávy identifikátor.

Zachování okna malé znamená, že méně id zpráv musí být zachována a uzavřeno a propustnost je ovlivněna méně. U entit s vysokou propustností, které vyžadují vyhledávání duplicit, byste měli zachovat okno co nejmenší.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Ve scénářích, kde klientský kód není schopen znovu odeslat zprávu se stejným *MessageId* jako dříve, je důležité navrhnout zprávy, které lze bezpečně znovu zpracovat. Tento [blogový příspěvek o idempotenci](https://particular.net/blog/what-does-idempotent-mean) popisuje různé techniky, jak to udělat.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
