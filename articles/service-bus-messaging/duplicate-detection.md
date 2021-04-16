---
title: Zjišťování duplicitních zpráv Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak můžete zjišťovat duplicity v Azure Service Busch zprávách. Duplicitní zprávu lze ignorovat a vyřadit.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499474"
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

Kromě toho, že stačí pouze povolit detekci duplicit, můžete také nakonfigurovat velikost časového intervalu historie vyhledávání duplicit, během kterého se uchovávají ID zpráv.
Tato hodnota je standardně 10 minut pro fronty a témata, přičemž minimální hodnota je 20 sekund až do maximální hodnoty 7 dní.

Povolení Detekce duplicitních dat a velikosti okna má přímý vliv na propustnost fronty (a tématu), protože všechna zaznamenaná ID zpráv musí odpovídat nově zadanému identifikátoru zprávy.

Udržování malého okna znamená, že se musí uchovávat a odpovídat méně identifikátorů zpráv a propustnost má dopad na míň. U entit s vysokou propustností, které vyžadují detekci duplicit, byste měli okno ponechat co nejmenší.

### <a name="using-the-portal"></a>Použití portálu

V portálu je při tvorbě entit zapnutá funkce Detekce duplicitních dat pomocí zaškrtávacího políčka **Povolit detekci duplicit** , která je ve výchozím nastavení vypnutá. Nastavení pro vytváření nových témat je ekvivalentní.

![Snímek obrazovky dialogového okna vytvořit front s vybranou možností povolit detekci duplicit a popsaný červeně][1]

> [!IMPORTANT]
> Po vytvoření fronty nelze povolit nebo zakázat detekci duplicit. Tuto možnost můžete provést pouze v době vytváření fronty. 

Časové období historie vyhledávání duplicit lze změnit v okně vlastnosti fronty a tématu v Azure Portal.

![Snímek obrazovky funkce Service Bus s zvýrazněným nastavením vlastností a možností historie vyhledávání duplicit zobrazený červeně][2]

### <a name="using-sdks"></a>S využitím sad SDK

Můžete použít kteroukoli z našich sad SDK napříč platformami .NET, Java, JavaScript, Python a přejít na povolení funkce zjišťování duplicitních dat při vytváření front a témat. Můžete také změnit časový interval historie vyhledávání duplicit.
K tomu, jaké vlastnosti se mají aktualizovat při vytváření front a témat k dosažení těchto akcí patří:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Všimněte si, že i když jsou názvy vlastností k dispozici v jazyce Pascal, jazyk JavaScript a sady SDK Pythonu budou používat ve stylu CamelCase velká a Snake velká písmena v uvedeném pořadí.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Ve scénářích, kdy kód klienta nemůže znovu odeslat zprávu se stejným parametrem *MessageID* jako předtím, je důležité navrhovat zprávy, které lze bezpečně znovu zpracovat. Tento [Blogový příspěvek o idempotence](https://particular.net/blog/what-does-idempotent-mean) popisuje různé postupy, jak to provést.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
