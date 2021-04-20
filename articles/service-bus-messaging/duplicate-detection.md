---
title: Zjišťování duplicitních zpráv Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak můžete zjišťovat duplicity v Azure Service Busch zprávách. Duplicitní zprávu lze ignorovat a vyřadit.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751269"
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


## <a name="duplicate-detection-window-size"></a>Velikost okna zjišťování duplicit

Kromě toho, že stačí pouze povolit detekci duplicit, můžete také nakonfigurovat velikost časového intervalu historie vyhledávání duplicit, během kterého se uchovávají ID zpráv.
Tato hodnota je standardně 10 minut pro fronty a témata, přičemž minimální hodnota je 20 sekund až do maximální hodnoty 7 dní.

Povolení Detekce duplicitních dat a velikosti okna má přímý vliv na propustnost fronty (a tématu), protože všechna zaznamenaná ID zpráv musí odpovídat nově zadanému identifikátoru zprávy.

Udržování malého okna znamená, že se musí uchovávat a odpovídat méně identifikátorů zpráv a propustnost má dopad na míň. U entit s vysokou propustností, které vyžadují detekci duplicit, byste měli okno ponechat co nejmenší.

## <a name="next-steps"></a>Další kroky
Můžete povolit detekci duplicitních zpráv pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Správce prostředků šablony, .NET, Java, Python a JavaScriptu. Další informace najdete v tématu [Povolení Detekce duplicitních zpráv](enable-duplicate-detection.md). 

Ve scénářích, kdy kód klienta nemůže znovu odeslat zprávu se stejným parametrem *MessageID* jako předtím, je důležité navrhovat zprávy, které lze bezpečně znovu zpracovat. Tento [Blogový příspěvek o idempotence](https://particular.net/blog/what-does-idempotent-mean) popisuje různé postupy, jak to provést.

Vyzkoušejte si ukázky v jazyce podle vašeho výběru, abyste prozkoumali Azure Service Bus funkce. 

- [Azure Service Bus ukázek klientských knihoven pro Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus ukázek klientských knihoven pro Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus ukázek klientských knihoven pro JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus Ukázky klientské knihovny pro TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Ukázky Azure. Messaging. ServiceBus pro .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Vyhledejte ukázky pro starší klientské knihovny .NET a Java níže:
- [Ukázky Microsoft. Azure. ServiceBus pro .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Ukázky Azure-ServiceBus pro Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

