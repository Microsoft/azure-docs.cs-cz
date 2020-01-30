---
title: Azure Service Bus úrovně Premium a Standard
description: Tento článek popisuje úrovně Standard a Premium Azure Service Bus. Porovná tyto úrovně a poskytuje technické rozdíly.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774567"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Úrovně zasílání zpráv Service Bus Premium a Standard

Zasílání zpráv Service Bus, které zahrnuje entity jako jsou fronty a témata, v sobě kombinuje funkce pro zasílání zpráv v rámci podniku s bohatou sémantikou publikování a odběru na úrovni cloudu. Zasílání zpráv Service Bus se používá jako páteřní prvek mnoha sofistikovaných cloudových řešení.

Úroveň *Premium* zasílání zpráv Service Bus řeší běžné požadavky zákazníků z pohledu rozsahu, výkonu a dostupnosti pro klíčové aplikace. Pro produkční scénáře se doporučuje úroveň Premium. Přestože mají tyto dvě úrovně skoro stejné sady funkcí, jsou určené pro použití v odlišných situacích.

V následující tabulce je zvýrazněno několik nejvýraznějších rozdílů.

| Premium | Úroveň Standard |
| --- | --- |
| Vysoká propustnost |Variabilní propustnost |
| Předvídatelný výkon |Variabilní latence |
| Pevné ceny |Variabilní průběžná cena |
| Možnost vertikálně navýšit a snížit kapacitu |Nevztahuje se |
| Velikost zprávy do 1 MB |Velikost zprávy do 256 kB |

**Zasílání zpráv Service Bus Premium** zajišťuje izolaci prostředků na úrovni CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Pro každý obor názvů Service Bus Premium si můžete koupit 1, 2, 4 nebo 8 jednotek zasílání zpráv. Jedna úloha nebo entita může zabírat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv se dá změnit na. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus.

Vedle toho, že je tento výkon předvídatelnější, je také rychlejší. Zasílání zpráv Service Bus úrovně Premium staví na databázovém jádru představeném ve službě [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Se zasíláním zpráv na úrovni Premium je výkon ve špičce mnohem vyšší než na úrovni Standard.

## <a name="premium-messaging-technical-differences"></a>Technické rozdíly zasílání zpráv na úrovni Premium

V následujících částech je uvedeno několik rozdílů mezi úrovněmi zasílání zpráv Premium a Standard.

### <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

V Zasílání zpráv na úrovni Premium se dělené fronty a témata nepodporují. Další informace o dělení najdete v oddílu [Dělené fronty a témata](service-bus-partitioning.md).

### <a name="express-entities"></a>Expresní entity

Protože zasílání zpráv úrovně Premium běží v kompletně izolovaném prostředí, nejsou expresní entity v oborech názvů úrovně Premium podporované. Další informace o expresní funkci najdete v popisu vlastnosti [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Pokud je váš kód spuštěný v rámci zasílání zpráv úrovně Standard a chcete přejít na úroveň Premium, ověřte, že vlastnost [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) je nastavena na hodnotu **false** (výchozí hodnota).

## <a name="premium-messaging-resource-usage"></a>Využívání prostředků pro zasílání zpráv na úrovni Premium
Obecně platí, že jakákoli operace s entitou může způsobit využití procesoru a paměti. Tady jsou některé z těchto operací: 

- Operace správy, například operace CRUD (vytváření, načítání, aktualizace a odstraňování) ve frontách, tématech a předplatných.
- Běhové operace (odesílání a příjem zpráv)
- Monitorování operací a výstrah

Další využití procesoru a paměti se navíc neúčtují. U úrovně zasílání zpráv na úrovni Premium má jednotka zprávy jednu cenu.

Využití CPU a paměti se sleduje a zobrazí se vám z následujících důvodů: 

- Zajištění transparentnosti interních systémů
- Pochopení kapacity zakoupených prostředků.
- Plánování kapacity, které vám pomůže se rozhodnout o horizontální navýšení nebo snížení kapacity.

## <a name="messaging-unit---how-many-are-needed"></a>Jednotka zasílání zpráv – kolik je potřeba?

Při zřizování oboru názvů Azure Service Bus Premium je nutné zadat počet přidělených jednotek zasílání zpráv. Tyto jednotky pro zasílání zpráv jsou vyhrazené prostředky, které jsou přiděleny oboru názvů.

Počet jednotek zasílání zpráv přidělených oboru názvů Service Bus Premium se dá **dynamicky upravovat** na faktoru změny (zvýšení nebo snížení) v úlohách.

Při rozhodování o počtu jednotek zasílání zpráv pro vaši architekturu je potřeba vzít v úvahu několik faktorů:

- Začněte s ***1 nebo 2 jednotkami pro zasílání zpráv*** přidělenou vašemu oboru názvů.
- Prostudujte metriky využití CPU v rámci [metrik využití prostředků](service-bus-metrics-azure-monitor.md#resource-usage-metrics) pro váš obor názvů.
    - Pokud je využití procesoru ***menší než 20%***, možná budete moci ***škálovat*** počet jednotek zasílání zpráv přidělených vašemu oboru názvů.
    - Pokud je využití procesoru ***nad 70%***, bude vaše aplikace využívat ***škálování*** počtu jednotek zasílání zpráv, které jsou přiděleny vašemu oboru názvů.

Proces škálování prostředků, které jsou přidělené oborům názvů Service Bus, se dá automatizovat pomocí [Azure Automation sad Runbook](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> **Škálování** prostředků přidělených oboru názvů může být buď přepnuto, nebo reaktivní.
>
>  * **Nemožnost: Pokud**je očekáváno další zatížení (z důvodu sezónnost nebo trendů), můžete pokračovat v přidělování více jednotek zasílání zpráv do oboru názvů před dosažením zatížení.
>
>  * **Reactive**: Pokud se při studiu metrik využití prostředků identifikují další úlohy, dají se k oboru názvů přidělit další prostředky, které budou zahrnovat rostoucí poptávku.
>
> Měřiče fakturace pro Service Bus jsou každou hodinu. V případě horizontálního navýšení kapacity platíte jenom za další prostředky za hodiny, které se používaly.
>

## <a name="get-started-with-premium-messaging"></a>Začínáme se zasíláním zpráv na úrovni Premium

Využití zasílání zpráv na úrovni Premium je jednoduché a je podobné standardnímu zasílání zpráv. Začněte [vytvořením oboru názvů](service-bus-create-namespace-portal.md) na webu [Azure Portal](https://portal.azure.com). Zkontrolujte, že jste v části **Cenová úroveň** vybrali **Premium**. Kliknutím na **Zobrazit všechny podrobnosti o cenách** zobrazíte další informace o jednotlivých úrovních.

![create-premium-namespace][create-premium-namespace]

Můžete také vytvářet [obory názvů Premium pomocí šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Další kroky

Pokud se o zasílání zpráv Service Bus chcete dozvědět více, podívejte se na následující odkazy:

* [Představení zasílání zpráv Azure Service Bus úrovně Premium (příspěvek na blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Představení zasílání zpráv Azure Service Bus úrovně Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled zasílání zpráv Service Bus](service-bus-messaging-overview.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
