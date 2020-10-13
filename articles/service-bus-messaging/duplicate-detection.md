---
title: Zjišťování duplicitních zpráv Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak můžete zjišťovat duplicity v Azure Service Busch zprávách. Duplicitní zprávu lze ignorovat a vyřadit.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dbca1b4b4f894d35835e7d37e0b4e742a2d3b917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083884"
---
# <a name="duplicate-detection"></a>Vyhledávání duplicit

Pokud dojde k selhání aplikace z důvodu závažné chyby hned po odeslání zprávy a opětovná instance aplikace se omylem domnívá, že předchozí doručení zprávy neproběhlo, pak následné odeslání způsobí, že se stejná zpráva zobrazí v systému dvakrát.

Je také možné, že došlo k chybě na úrovni klienta nebo sítě a odeslání zprávy do fronty se zaznamená potvrzením, že se potvrzení neúspěšně vrátilo klientovi. Tento scénář opouští klientovi pochybnosti o výsledku operace odeslání.

Zjišťování duplicitních hodnot trvá z těchto případů pochybnostm, protože umožňuje odesílateli znovu odeslat stejnou zprávu a fronta nebo téma zahodí jakékoli duplicitní kopie.

Povolení Detekce duplicitních dat pomáhá sledovat, které zprávy *MessageID* řízené aplikací všech zpráv odeslaných do fronty nebo tématu během zadaného časového období. Pokud se pošle nějaká nová zpráva s parametrem *MessageID* zaznamenaným během časového intervalu, zpráva se nahlásí jako přijatá (operace odeslání se zdaří), ale nově odeslaná zpráva se okamžitě ignoruje a vynechá. Neberou v úvahu žádné jiné části jiné zprávy, než je *MessageID* .

Řízení aplikací identifikátoru je zásadní, protože pouze to umožňuje aplikaci spojit rozhraní *MessageID* s kontextem obchodního procesu, ze kterého může být v případě chyby předvídatelné rekonstruována.

Pro obchodní proces, ve kterém se v průběhu zpracování určitého kontextu aplikace odesílají různé zprávy, může být parametr *MessageID* složený z identifikátoru kontextu na úrovni aplikace, jako je číslo nákupní objednávky a předmět zprávy, například **12345.2017/platba**.

Parametr *MessageID* může mít vždy nějaký identifikátor GUID, ale ukotvení identifikátoru k obchodnímu procesu vede k předvídatelnému opakování, což je žádoucí pro efektivní využití funkce zjišťování duplicitních hodnot.

> [!NOTE]
> Pokud je povolená detekce duplicit a ID relace nebo klíč oddílu nejsou nastavené, použije se jako klíč oddílu ID zprávy. Pokud není ID zprávy také nastaveno, knihovny .NET a AMQP automaticky vygenerují ID zprávy pro zprávu. Další informace najdete v tématu [použití klíčů oddílů](service-bus-partitioning.md#use-of-partition-keys).

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
