---
title: Automatické předávání Azure Service Bus entit zasílání zpráv
description: Tento článek popisuje, jak zřetězit frontu Azure Service Bus nebo předplatné do jiné fronty nebo tématu.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678984"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Zřetězení Service Bus entit pomocí procesu autopřesměrovávání

Funkce *autopřesměrovávání* Service Bus umožňuje řetězit frontu nebo předplatné do jiné fronty nebo tématu, které je součástí stejného oboru názvů. Pokud je povoleno automatické přeposílání, Service Bus automaticky odebere zprávy, které jsou umístěny v první frontě nebo předplatném (zdroj), a umístí je do druhé fronty nebo tématu (cíle). Je stále možné odeslat zprávu cílové entitě přímo.

> [!NOTE]
> Základní Service Bus úrovně nepodporuje funkci dodávání. Úroveň Standard a Premium tuto funkci podporuje. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-autoforwarding"></a>Použití autopřesměrovávání

Můžete povolit autopřesměrovávání nastavením vlastností [QueueDescription. ForwardTo][QueueDescription.ForwardTo] nebo [SubscriptionDescription. ForwardTo][SubscriptionDescription.ForwardTo] na objektech [QueueDescription][QueueDescription] nebo [SubscriptionDescription][SubscriptionDescription] pro zdroj, jak je uvedeno v následujícím příkladu:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Cílová entita musí existovat v okamžiku, kdy je zdrojová entita vytvořena. Pokud cílová entita neexistuje, Service Bus vrátí výjimku, pokud se zobrazí výzva k vytvoření zdrojové entity.

K horizontálnímu navýšení kapacity jednotlivých témat můžete použít automatické přeposílání. Service Bus omezuje [počet předplatných v daném tématu](service-bus-quotas.md) na 2 000. Další předplatná můžete přizpůsobit vytvořením témat druhé úrovně. I v případě, že nejste vázáni omezením Service Bus pro počet předplatných, může přidání druhé úrovně témat zlepšit celkovou propustnost vašeho tématu.

![Diagram scénáře automatického přeposílání znázorňující zprávu zpracovaná prostřednictvím tématu Orders, které lze rozvětvit na některé tři témata druhé úrovně objednávky.][0]

K oddálení odesílatelů zpráv z přijímačů můžete použít také autopřesměrovávání. Představte si třeba systém ERP, který se skládá ze tří modulů: zpracování objednávek, Správa inventáře a Správa vztahů se zákazníky. Každý z těchto modulů generuje zprávy, které jsou zařazeny do odpovídajícího tématu. Alice a Bob jsou obchodní zástupci, kteří mají zájem o všechny zprávy, které se vztahují ke svým zákazníkům. Chcete-li přijímat tyto zprávy, Alice a Bob vytvoří osobní frontu a předplatné pro každé téma ERP, které automaticky předají všechny zprávy do fronty.

![Diagram scénáře pro autopřesměrovávání zobrazující tři moduly zpracování, které odesílají zprávy prostřednictvím tří odpovídajících témat do dvou samostatných front.][1]

Pokud Alice přijde na dovolenou, jeho osobní fronta místo tématu ERP vyplní. V tomto scénáři vzhledem k tomu, že prodejní zástupce neobdržel žádné zprávy, není k dispozici žádná z témat pro ERP.

> [!NOTE]
> Pokud je nastaveno automatické přeposílání, hodnota AutoDeleteOnIdle na **zdrojovém i cílovém umístění** je automaticky nastavena na maximální hodnotu datového typu.
> 
>   - Funkce autopřesměrovává na straně zdroje funguje jako operace Receive. Proto zdroj, který má nastavení pro přeposílání, není nikdy ve skutečnosti "nečinný".
>   - Na straně cíle je to, aby se zajistilo, že je vždy cíl, na který se zpráva přepošle.

## <a name="autoforwarding-considerations"></a>Předpoklady pro autopřesměrovávání

Pokud cílová entita načítá příliš mnoho zpráv a překračuje kvótu, nebo je cílová entita zakázaná, přidá zdrojová entita zprávy do [fronty nedoručených](service-bus-dead-letter-queues.md) zpráv, dokud v cílovém umístění není mezera (nebo je entita znovu povolená). Tyto zprávy pokračují v provozu ve frontě nedoručených zpráv, takže je musíte explicitně přijímat a zpracovávat z fronty nedoručených zpráv.

Při zřetězení jednotlivých témat k získání složeného tématu s mnoha předplatnými doporučujeme, abyste měli k dispozici střední počet předplatných na první úrovni a celou řadu předplatných v tématech druhé úrovně. Například téma první úrovně s 20 předplatnými, každý z nich zřetězené s podmnožinou druhé úrovně s 200 odběry, umožňuje vyšší propustnost než v tématu první úrovně s 200 předplatnými, každý zřetězení k druhému tématu s 20 odběry.

Service Bus účtuje jednu operaci pro každou předanou zprávu. Například odeslání zprávy do tématu s 20 odběry, každé z nich nakonfigurované na automatického přeposílání zpráv do jiné fronty nebo tématu se účtuje jako 21 operací, pokud všechna předplatná na první úrovni obdrží kopii zprávy.

Aby bylo možné vytvořit odběr zřetězený s jinou frontou nebo tématem, musí mít tvůrce předplatného oprávnění **Spravovat** ke zdrojové i cílové entitě. Odesílání zpráv do zdrojového tématu vyžaduje pouze oprávnění **Odeslat** ve zdrojovém tématu.

Nevytvářejte řetěz, který překračuje 4 segmenty směrování. Zprávy, které překračují 4 segmenty směrování, jsou nedoručené.

## <a name="next-steps"></a>Další kroky

Podrobné informace o autopřesměrovávání najdete v následujících referenčních tématech:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Další informace o vylepšení výkonu Service Bus najdete v tématu. 

* [Doporučené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](service-bus-performance-improvements.md)
* [Dělené entity zasílání zpráv][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
