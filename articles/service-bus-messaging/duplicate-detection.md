---
title: Detekce duplicitních zpráv Azure Service Bus | Microsoft Docs
description: Zjistit duplicitní sběrnice zpráv
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: efc5608d4812edbb3f477dffbc2b495b331bd787
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198549"
---
# <a name="duplicate-detection"></a>Detekce duplicitních

Pokud aplikace zaznamená ihned po závažné chybě odešle zprávu a instance restartovat aplikace chybnou informací dochází k závěru, že doručení předchozí zpráv neuskutečnilo, následné odesílání způsobí, že tato zpráva se objeví v systému dvakrát.

Je také možné pro chybu na úrovni klienta nebo sítě proběhnout chvíli dříve, a pro odeslané zprávy, která se potvrdí do fronty, s potvrzení nebylo úspěšně vrácen do klienta. Tento scénář zanechává klienta nejistých dotázán na výsledek operaci odeslání.

Detekce duplicitních trvá nejistých mimo těchto situacích povolením odesílatele znovu odeslat stejnou zprávu a fronta nebo téma zahodí všechny duplicitní kopie.

Povolení detekce duplicitních pomáhá udržovat přehled o řízené aplikací *MessageId* všech zpráv odeslaných do fronty nebo téma během zadaného časového období. Pokud žádné nové zprávy dělal *MessageId* který již byl zaprotokolován během časový interval, zprávy hlášení jako přijato (operaci odeslání úspěšné), ale nově odeslaná zpráva je okamžitě ignorována a vyřadit. Žádné další části zprávy jinak než *MessageId* jsou považovány za.

Řízení aplikace identifikátoru je důležité, protože pouze to, že umožňuje, aby aplikace ke svázání *MessageId* na obchodní proces kontext, ze kterého je možné předvídatelně zrekonstruovat podstatné v případě selhání.

Pro obchodní proces, v níž jsou odesílány více zpráv v průběhu zpracování kontextu některých aplikací *MessageId* může být složené identifikátoru kontextu úrovni aplikace, jako je například číslo objednávky a předmět zprávy; například **12345.2017/platby**.

*MessageId* může být vždy některé identifikátor GUID, ale ukotvení identifikátor, který obchodní proces vypočítá předvídatelný opakovatelnosti, která se požaduje efektivně využívat funkce vyhledávání duplicit.

## <a name="enable-duplicate-detection"></a>Povolit vyhledávání duplicit

Na portálu, je zapnutá funkce během vytváření entit s **povolení detekce duplicitních** zaškrtávací políčko, které ve výchozím nastavení. Nastavení pro vytvoření nová témata je ekvivalentní.

![][1]

Prostřednictvím kódu programu, nastavte příznak s [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) vlastnost plného rozhraní .NET API. Pomocí rozhraní API služby Správce prostředků Azure, je hodnota nastavená s [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnost.

Historie čas detekce duplicitních výchozí nastavení je 30 sekund pro fronty a témata, přičemž maximální hodnota je 7 dní. Můžete změnit toto nastavení v okně vlastností frontu a téma na portálu Azure.

![][2]

Prostřednictvím kódu programu, můžete nakonfigurovat velikost okna detekci duplikátů, během kterého se zachovají identifikátory zpráv, [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) vlastnost s úplné rozhraní API rozhraní .NET Framework . Pomocí rozhraní API služby Správce prostředků Azure, je hodnota nastavená s [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnost.

Všimněte si, že povolení detekce duplicitních a velikosti okna přímo vliv propustnost fronty (a téma), protože všechny zaznamenané ID zprávy musí být porovnání identifikátor nově odeslané zprávy.

Zachování okno malé znamená, že musí být méně identifikátory zpráv uchována a matched a propustnost je dopad na menší. Pro vysoké propustnosti entity, které vyžadují detekci duplikátů byste měli mít okno co nejmenší.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
