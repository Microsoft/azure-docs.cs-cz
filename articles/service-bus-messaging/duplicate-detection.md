---
title: Zjišťování duplicitních zpráv Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak můžete zjišťovat duplicity v Azure Service Busch zprávách. Duplicitní zprávu lze ignorovat a vyřadit.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 8ff98b3a052be6004a2dc070f10d6f8c9ca0617f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684804"
---
# <a name="duplicate-detection"></a>Vyhledávání duplicit

Pokud dojde k selhání aplikace z důvodu závažné chyby hned po odeslání zprávy a opětovná instance aplikace se omylem domnívá, že předchozí doručení zprávy neproběhlo, pak následné odeslání způsobí, že se stejná zpráva zobrazí v systému dvakrát.

Je také možné, že došlo k chybě na úrovni klienta nebo sítě a odeslání zprávy do fronty se zařadí do fronty s potvrzením, které se neúspěšně vrátilo klientovi. Tento scénář opouští klientovi pochybnosti o výsledku operace odeslání.

Zjišťování duplicitních hodnot trvá z těchto případů pochybnostm, protože umožňuje odesílateli znovu odeslat stejnou zprávu a fronta nebo téma zahodí jakékoli duplicitní kopie.

> [!NOTE]
> Základní vrstva Service Bus nepodporuje detekci duplicit. Úrovně Standard a Premium podporují detekci duplicit. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Jak to funguje? 
Povolení Detekce duplicitních dat pomáhá sledovat, které zprávy *MessageID* řízené aplikací všech zpráv odeslaných do fronty nebo tématu během zadaného časového období. Pokud se pošle nějaká nová zpráva s parametrem *MessageID* zaznamenaným během časového intervalu, zpráva se nahlásí jako přijatá (operace odeslání se zdaří), ale nově odeslaná zpráva se okamžitě ignoruje a vynechá. Neberou v úvahu žádné jiné části jiné zprávy, než je *MessageID* .

Řízení aplikací identifikátoru je zásadní, protože pouze to umožňuje aplikaci spojit rozhraní *MessageID* s kontextem obchodního procesu, ze kterého může být v případě chyby předvídatelné rekonstruována.

Pro obchodní proces, ve kterém se v průběhu zpracování určitého kontextu aplikace odesílají různé zprávy, může být parametr *MessageID* složený z identifikátoru kontextu na úrovni aplikace, jako je číslo nákupní objednávky a předmět zprávy, například **12345.2017/platba**.

Parametr *MessageID* může mít vždy nějaký identifikátor GUID, ale ukotvení identifikátoru k obchodnímu procesu vede k předvídatelnému opakování, což je žádoucí pro efektivní použití funkce zjišťování duplicitních hodnot.

> [!IMPORTANT]
>- Pokud je **povoleno** **dělení na oddíly** , `MessageId+PartitionKey` slouží k určení jedinečnosti. Pokud jsou povoleny relace, klíč oddílu a ID relace musí být stejné. 
>- Pokud je **rozdělení na oddíly** **zakázáno** (výchozí nastavení), `MessageId` slouží pouze k určení jedinečnosti.
>- Informace o identifikátorech SessionId, PartitionKey a MessageId najdete v tématu [použití klíčů oddílů](service-bus-partitioning.md#use-of-partition-keys).
>- [Úroveň Premier](service-bus-premium-messaging.md) nepodporuje dělení na oddíly, proto doporučujeme, abyste ve svých aplikacích používali jedinečná ID zpráv a nespoléhá se na klíče oddílů pro detekci duplicitních dat. 


## <a name="enable-duplicate-detection"></a>Povolit detekci duplicit

Tato funkce je v portálu při vytváření entit zapnutá pomocí zaškrtávacího políčka **Povolit detekci duplicit** , která je ve výchozím nastavení vypnutá. Nastavení pro vytváření nových témat je ekvivalentní.

![Snímek obrazovky dialogového okna vytvořit front s vybranou možností povolit detekci duplicit a popsaný červeně][1]

> [!IMPORTANT]
> Po vytvoření fronty nelze povolit nebo zakázat detekci duplicit. Tuto možnost můžete provést pouze v době vytváření fronty. 

Prostřednictvím kódu programu nastavíte příznak s vlastností [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) v plném rozhraní API .NET Framework. V rozhraní Azure Resource Manager API je hodnota nastavena pomocí vlastnosti [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Časová historie vyhledávání duplicit ve výchozím nastavení je 30 sekund pro fronty a témata s maximální hodnotou 7 dní. Toto nastavení můžete změnit v okně fronta a vlastnosti tématu v Azure Portal.

![Snímek obrazovky funkce Service Bus s zvýrazněným nastavením vlastností a možností historie vyhledávání duplicit zobrazený červeně][2]

Prostřednictvím kódu programu můžete nakonfigurovat velikost okna zjišťování duplicitních dat, během kterého se uchovávají ID zpráv, pomocí vlastnosti [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) s úplným .NET Framework API. V rozhraní Azure Resource Manager API je hodnota nastavena pomocí vlastnosti [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Povolení Detekce duplicitních dat a velikosti okna má přímý vliv na propustnost fronty (a tématu), protože všechna zaznamenaná ID zpráv musí odpovídat nově zadanému identifikátoru zprávy.

Udržování malého okna znamená, že se musí uchovávat a odpovídat méně identifikátorů zpráv a propustnost má dopad na míň. U entit s vysokou propustností, které vyžadují detekci duplicit, byste měli okno ponechat co nejmenší.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Ve scénářích, kdy kód klienta nemůže znovu odeslat zprávu se stejným parametrem *MessageID* jako předtím, je důležité navrhovat zprávy, které lze bezpečně znovu zpracovat. Tento [Blogový příspěvek o idempotence](https://particular.net/blog/what-does-idempotent-mean) popisuje různé postupy, jak to provést.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
