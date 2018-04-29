---
title: Přehled cenových úrovní zasílání zpráv Azure Service Bus Premium a Standard | Dokumentace Microsoftu
description: Úrovně zasílání zpráv Service Bus Premium a Standard
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: ''
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: sethm
ms.openlocfilehash: cf750f451351f729296991499f233b235b27a5e7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Úrovně zasílání zpráv Service Bus Premium a Standard

Zasílání zpráv Service Bus, které zahrnuje entity jako jsou fronty a témata, v sobě kombinuje funkce pro zasílání zpráv v rámci podniku s bohatou sémantikou publikování a odběru na úrovni cloudu. Zasílání zpráv Service Bus se používá jako páteřní prvek mnoha sofistikovaných cloudových řešení.

Úroveň *Premium* zasílání zpráv Service Bus řeší běžné požadavky zákazníků z pohledu rozsahu, výkonu a dostupnosti pro klíčové aplikace. Přestože mají tyto dvě úrovně skoro stejné sady funkcí, jsou určené pro použití v odlišných situacích.

V následující tabulce je zvýrazněno několik nejvýraznějších rozdílů.

| Premium | Standard |
| --- | --- |
| Vysoká propustnost |Variabilní propustnost |
| Předvídatelný výkon |Variabilní latence |
| Pevné ceny |Variabilní průběžná cena  |
| Možnost vertikálně navýšit a snížit kapacitu |neuvedeno |
| Velikost zprávy do 1 MB |Velikost zprávy do 256 kB |

**Zasílání zpráv Service Bus Premium** zajišťuje izolaci prostředků na úrovni CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Pro každý obor názvů Service Bus Premium můžete koupit 1, 2 nebo 4 jednotky zasílání zpráv. Jedna úloha nebo entita může zabírat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv se dá změnit podle libosti, ale fakturuje se podle 24hodinoví/denní sazby. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus.

Vedle toho, že je tento výkon předvídatelnější, je také rychlejší. Zasílání zpráv Service Bus úrovně Premium staví na databázovém jádru představeném ve službě [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Se zasíláním zpráv na úrovni Premium je výkon ve špičce mnohem vyšší než na úrovni Standard.

## <a name="premium-messaging-technical-differences"></a>Technické rozdíly zasílání zpráv na úrovni Premium

V následujících částech je uvedeno několik rozdílů mezi úrovněmi zasílání zpráv Premium a Standard.

### <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Dělené fronty a témata se podporují v zasílání zpráv úrovně Premium; ve skutečnosti jsou tyto entity vždy dělené (a nelze je vypnout). Dělené fronty a témata úrovně Premium ale nefungují na stejném principu jako při zasílání zpráv Service Bus na úrovni Standard. Zasílání zpráv na úrovni Premium nepoužívá úložiště dat SQL a není tu tak možnost soupeření o prostředky, které je obvyklé na sdílené platformě. To znamená, že dělení není pro zvýšení výkonu potřeba. Počet oddílů na úrovni Premium se navíc snížil z 16, které jsou na úrovni Standard, na 2. Dva oddíly zajišťují dostupnost, navíc je to vhodnější počet pro prostředí runtime úrovně Premium. 

Pokud u zasílání zpráv úrovně Premium zadáte velikost entity pomocí [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes), rozdělí se tato velikost rovným dílem mezi 2 oddíly, na rozdíl od [standardních dělených entit](service-bus-partitioning.md#standard), u kterých je celková velikost 16násobek zadané velikosti. 

Další informace o dělení najdete v oddílu [Dělené fronty a témata](service-bus-partitioning.md).

### <a name="express-entities"></a>Expresní entity

Protože zasílání zpráv úrovně Premium běží v kompletně izolovaném prostředí, nejsou expresní entity v oborech názvů úrovně Premium podporované. Další informace o expresní funkci najdete v popisu vlastnosti [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Pokud je váš kód spuštěný v rámci zasílání zpráv úrovně Standard a chcete přejít na úroveň Premium, ověřte, že vlastnost [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) je nastavena na hodnotu **false** (výchozí hodnota).

## <a name="get-started-with-premium-messaging"></a>Začínáme se zasíláním zpráv na úrovni Premium

Využití zasílání zpráv na úrovni Premium je jednoduché a je podobné standardnímu zasílání zpráv. Začněte [vytvořením oboru názvů](service-bus-create-namespace-portal.md) na webu [Azure Portal](https://portal.azure.com). Zkontrolujte, že jste v části **Volba cenové úrovně** vybrali **Premium**.

![create-premium-namespace][create-premium-namespace]

Můžete také vytvářet [obory názvů Premium pomocí šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Další kroky

Pokud se o zasílání zpráv Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Představení zasílání zpráv Azure Service Bus úrovně Premium (příspěvek na blogu)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Představení zasílání zpráv Azure Service Bus úrovně Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled zasílání zpráv Service Bus](service-bus-messaging-overview.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
