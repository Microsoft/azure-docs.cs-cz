---
title: Automatické předávání entit zasílání zpráv Azure Service Bus
description: Tento článek popisuje, jak zřetězit frontu Azure Service Bus nebo předplatné do jiné fronty nebo tématu.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761045"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Řetězení entit service bus s automatickým předáváním

Funkce *automatického předávání služby* Service Bus umožňuje zřetězit frontu nebo odběr do jiné fronty nebo tématu, které je součástí stejného oboru názvů. Je-li povoleno automatické předávání, služba Service Bus automaticky odebere zprávy, které jsou umístěny v první frontě nebo předplatném (zdroj), a umístí je do druhé fronty nebo tématu (cíl). Stále je možné odeslat zprávu cílové entitě přímo.

## <a name="using-autoforwarding"></a>Použití automatického předávání

Automatické předávání můžete povolit nastavením [vlastností QueueDescription.ForwardTo][QueueDescription.ForwardTo] nebo [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] na objektech [QueueDescription][QueueDescription] nebo [SubscriptionDescription][SubscriptionDescription] pro zdroj, jako v následujícím příkladu:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Cílová entita musí existovat v době vytvoření zdrojové entity. Pokud cílová entita neexistuje, service bus vrátí výjimku, když je požádán o vytvoření zdrojové entity.

Automatické předávání můžete použít k horizontálnímu navýšení kapacity jednotlivých témat. Service Bus omezuje [počet předplatných na dané téma na](service-bus-quotas.md) 2 000. Můžete přizpůsobit další odběry vytvořením témat druhé úrovně. I v případě, že nejste vázáni omezení service bus na počet odběrů, přidání druhé úrovně témat může zlepšit celkovou propustnost vašeho tématu.

![Scénář automatického předávání][0]

Automatické předávání můžete také použít k oddělení odesílatelů zpráv od příjemců. Zvažte například systém ERP, který se skládá ze tří modulů: zpracování objednávek, řízení zásob a řízení vztahů se zákazníky. Každý z těchto modulů generuje zprávy, které jsou zařazeny do odpovídajícího tématu. Alice a Bob jsou obchodní zástupci, kteří se zajímají o všechny zprávy, které se vztahují k jejich zákazníkům. Chcete-li přijímat tyto zprávy, Alice a Bob každý vytvořit osobní fronty a odběr na každé z témat ERP, které automaticky předávají všechny zprávy do fronty.

![Scénář automatického předávání][1]

Pokud Alice jede na dovolenou, zaplní se její osobní fronta, nikoli téma ERP. V tomto scénáři, protože obchodní zástupce neobdržel žádné zprávy, žádné z témat ERP nikdy dosáhnout kvóty.

> [!NOTE]
> Při nastavení automatického předávání je hodnota pro AutoDeleteOnIdle na **zdroj i cíl** je automaticky nastavena na maximální hodnotu datového typu.
> 
>   - Na straně zdroje automatické předávání funguje jako operace příjmu. Takže zdroj, který má nastavení automatického předávání, není nikdy opravdu "nečinný".
>   - Na cílové straně se provádí, aby bylo zajištěno, že je vždy cíl předat zprávu.

## <a name="autoforwarding-considerations"></a>Aspekty automatického předávání

Pokud cílová entita shromažďuje příliš mnoho zpráv a překračuje kvótu nebo je cílová entita zakázána, přidá zdrojová entita zprávy do [fronty nedoručených zpráv,](service-bus-dead-letter-queues.md) dokud není v cílovém umístění (nebo je entita znovu povolena). Tyto zprávy nadále žijí ve frontě nedoručených zpráv, takže je nutné explicitně přijímat a zpracovávat je z fronty nedoručených zpráv.

Při řetězení dohromady jednotlivá témata získat složené téma s mnoha odběry, je doporučeno mít mírný počet odběrů na téma první úrovně a mnoho předplatných na témata druhé úrovně. Například téma první úrovně s 20 předplatnými, z nichž každé je zřetězené na téma druhé úrovně s 200 předplatnými, umožňuje vyšší propustnost než téma první úrovně s 200 předplatnými, z nichž každé je zřetězené na téma druhé úrovně s 20 předplatnými.

Service Bus účtuje jednu operaci pro každou předanou zprávu. Například odeslání zprávy tématu s 20 odběry, z nichž každý je nakonfigurován tak, aby automaticky předávaných zpráv do jiné fronty nebo tématu, se účtuje jako 21 operací, pokud všechny odběry první úrovně obdrží kopii zprávy.

Chcete-li vytvořit předplatné, které je zřetězené do jiné fronty nebo tématu, musí mít tvůrce předplatného oprávnění **spravovat** zdrojovou i cílovou entitu. Odesílání zpráv do zdrojového tématu vyžaduje pouze **oprávnění k odeslání** ve zdrojovém tématu.

## <a name="next-steps"></a>Další kroky

Podrobné informace o automatickém předávání informací naleznete v následujících referenčních tématech:

* [Vpřed][QueueDescription.ForwardTo]
* [Popis fronty][QueueDescription]
* [Popis předplatného][SubscriptionDescription]

Další informace o vylepšeních výkonu služby Service Bus najdete v tématu 

* [Doporučené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](service-bus-performance-improvements.md)
* [Oddíly zasílání zpráv entity][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
