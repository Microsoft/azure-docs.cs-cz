---
title: Filtry témat Azure Service Bus | Dokumentace Microsoftu
description: Filtrovat témat Azure Service Bus
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: ac30718a92d76dedcb5b0ef3bdd2f282dd117720
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48853517"
---
# <a name="topic-filters-and-actions"></a>Akce a filtry témat

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy jsou určeny ve formě jednoho nebo více pravidel s názvem předplatného. Každé pravidlo obsahuje podmínku, která vybere konkrétní zprávy a akci, která označí vybrané zprávy. Pro každou odpovídající podmínku pravidla odběr vytvoří kopii zprávy, která může pro každé odpovídající pravidlo obsahovat jiné poznámky.

Každé předplatné nově vytvořené téma má pravidlo počáteční výchozí předplatné. Pokud nechcete zadat explicitně podmínka pro pravidlo, je použitý filtr **true** filtr, který umožňuje výběr do předplatného, všechny zprávy. Výchozí pravidlo nemá žádné přidružené anotaci akce.

Service Bus podporuje tři podmínky filtru:

-   *Logické filtry* – **TrueFilter** a **FalseFilter** buď způsobit, že všechny příchozí zprávy (**true**) nebo žádné příchozí zprávy (**false**) má být vybrán pro předplatné.

-   *Filtry SQL* – **SqlFilter** obsahuje podmíněný výraz podobném SQL, který je vyhodnocen v broker před uživatelem definované vlastnosti a vlastnosti systému příchozí zprávy. Musí předcházet všem systémovým vlastnostem `sys.` v podmíněných výrazech. [Podmnožina jazyka SQL pro podmínky filtru](service-bus-messaging-sql-filter.md) testy existence vlastnosti (`EXISTS`), stejně jako u hodnoty null (`IS NULL`), logický operátor NOT/a/nebo relační operátory, jednoduché Číselná aritmetika a jednoduchý text porovnávání vzorů s `LIKE`.

-   *Korelační filtry* – **CorrelationFilter** obsahuje sadu podmínek, které jsou porovnány s nejméně jeden z vlastnosti uživatele a systémové opravovány zprávy. Běžné použití je tak, aby odpovídala **CorrelationId** vlastnost, ale aplikace můžete také zvolit odpovídající **ContentType**, **popisek**,  **ID zprávy**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **k**a jakýkoli definovaný uživatelem Vlastnosti. Odpovídající položka existuje při opravovány message s hodnotou pro vlastnost je rovna hodnotě zadané v korelační filtr. Pro řetězcové výrazy porovnání je velká a malá písmena. Při zadávání více vlastností shoda, filtr kombinuje je jako logická podmínka a, to znamená pro filtr tak, aby odpovídaly, všechny podmínky musí odpovídat.

Všechny filtry vyhodnocení vlastnosti zprávy. Filtry se nedá vyhodnotit tělo zprávy.

Komplexní filtrovací pravidla, která vyžadují kapacitu zpracování. Zejména použití pravidel filtru SQL za následek nižší celkovou propustnost zpráv na úrovni předplatného, tématu a obor názvů. Kdykoli je to možné, aplikace by měl vybrat korelační filtry více než podobném SQL filtrů, protože jsou mnohem výkonnější při zpracování a proto mít menší dopad na propustnost.

## <a name="actions"></a>Akce

Pomocí podmínky filtru SQL můžete definovat akce, která může opatřit poznámkami zprávy pomocí přidání, odebrání nebo výměna vlastností a jejich hodnot. Akce [používá výraz podobném SQL](service-bus-messaging-sql-filter.md) , volně leans na syntaxe příkazu SQL UPDATE. Akce se provádí na zprávu po mu odpovídala, a před zprávy do tématu. Změny vlastnosti zprávy jsou privátní pro zpráva zkopírována do předplatného.

## <a name="usage-patterns"></a>Vzory využití

Nejjednodušší scénář využití tématu je, že každé předplatné získá kopii každá zpráva odeslaná do tématu, který umožňuje vzoru všesměrového vysílání.

Filtry a akcí povolit dvě další skupiny vzory: vytváření oddílů a směrování.

Dělení používá filtry k distribuci zpráv do více stávajícím předplatným tématu způsobem zajišťuje předvídatelný a vzájemně vylučují. Dělení model se používá, když je systém škálované zpracování mnoho různých kontextech v funkčně stejný jako oddíly, že každý obsahovat podmnožinu celkového dat; například profil informace o zákaznících. Vydavatel s dělením, odešle zprávy do tématu nevyžaduje žádnou znalost dělení modelu. Zprávu je poté přesunut do správné předplatné, ze kterého se pak dá načíst pomocí obslužné rutiny zpráv oddíl.

Směrování používá filtry k distribuci zpráv do tématu předplatná předvídatelným způsobem, ale ne nutně exkluzivní. Ve spojení s [automatické přesměrování](service-bus-auto-forwarding.md) funkce, grafy tématu filtry je možné vytvořit komplexní směrování v rámci oboru názvů služby Service Bus pro distribuce zpráv v rámci oblasti Azure. S Azure Functions nebo Azure Logic Apps, který funguje jako most mezi obory názvů služby Azure Service Bus můžete vytvářet složité globální topologie díky přímé integraci do – obchodní aplikace.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)