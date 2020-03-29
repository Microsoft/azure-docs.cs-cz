---
title: Filtry tématu Azure Service Bus | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak mohou odběratelé definovat, které zprávy chtějí přijímat z tématu zadáním filtrů.
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
ms.date: 01/27/2020
ms.author: spelluru
ms.openlocfilehash: b8ffbb16763bfe6485ebf2ab770f4537ddbc8569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774495"
---
# <a name="topic-filters-and-actions"></a>Akce a filtry témat

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy se určují ve formě jednoho nebo více pojmenovaných pravidel odběru. Každé pravidlo se skládá z podmínky, která vybere konkrétní zprávy a akce, která anotuje vybranou zprávu. Pro každou odpovídající podmínku pravidla odběr vytvoří kopii zprávy, která může pro každé odpovídající pravidlo obsahovat jiné poznámky.

Každé nově vytvořené tematické předplatné má počáteční výchozí pravidlo předplatného. Pokud explicitně nezadáte podmínku filtru pro pravidlo, použitý filtr je **skutečný** filtr, který umožňuje vybrat všechny zprávy do předplatného. Výchozí pravidlo nemá žádnou přidruženou akci poznámky.

Service Bus podporuje tři podmínky filtru:

-   *Logické filtry* - **TrueFilter** a **FalseFilter** buď způsobit všechny příchozí zprávy **(true)** nebo žádný z příchozích zpráv (**false**) mají být vybrány pro odběr.

-   *SQL Filters* - **SqlFilter** obsahuje podmíněný výraz podobný SQL, který je vyhodnocen v zprostředkovateli proti vlastnostem definovaným uživatelem a vlastnostem systému příchozích zpráv. Všechny vlastnosti systému `sys.` musí být v podmíněném výrazu předponou. [Podmnožina jazyka SQL pro podmínky filtru](service-bus-messaging-sql-filter.md) testuje`EXISTS`existenci vlastností ( ),`IS NULL`stejně jako hodnoty null ( ), logické NOT/AND/OR, relační operátory, jednoduché číselné aritmetické a jednoduché porovnávání textových vzorů s `LIKE`.

-   *Korelační filtry* - **CorrelationFilter** obsahuje sadu podmínek, které jsou porovnány s jedním nebo více příchozí zprávy uživatele a vlastnosti systému. Běžné použití je porovnat s **Vlastnost CorrelationId,** ale aplikace můžete také zvolit, aby **odpovídaly ContentType**, **Label**, **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **To**a všechny vlastnosti definované uživatelem. Shoda existuje, pokud se hodnota příchozí zprávy pro vlastnost rovná hodnotě zadané ve filtru korelace. Pro řetězcové výrazy je porovnání rozlišováno malá a velká písmena. Při zadávání více vlastností shody je filtr kombinuje jako logickou podmínku AND, což znamená, že filtr odpovídá, musí se všechny podmínky shodovat.

Všechny filtry vyhodnocují vlastnosti zprávy. Filtry nemohou vyhodnotit text zprávy.

Komplexní pravidla filtru vyžadují kapacitu zpracování. Zejména použití pravidel filtru SQL má za následek nižší celkovou propustnost zpráv na úrovni předplatného, tématu a oboru názvů. Kdykoli je to možné, aplikace by měly zvolit filtry korelace přes filtry podobné SQL, protože jsou mnohem efektivnější při zpracování a proto mají menší dopad na propustnost.

## <a name="actions"></a>Akce

S podmínkami filtru SQL můžete definovat akci, která může oznamovat zprávu přidáním, odebráním nebo nahrazením vlastností a jejich hodnot. Akce [používá výraz podobný SQL,](service-bus-messaging-sql-filter.md) který se volně opírá o syntaxi příkazu SQL UPDATE. Akce se provádí na zprávu poté, co byla spárována a před zpráva je vybrána do předplatného. Změny vlastností zprávy jsou soukromé zprávy zkopírované do předplatného.

## <a name="usage-patterns"></a>Vzorce použití

Nejjednodušší scénář použití pro téma je, že každé předplatné získá kopii každé zprávy odeslané na téma, které umožňuje vzor vysílání.

Filtry a akce umožňují další dvě skupiny vzorů: dělení a směrování.

Dělení používá filtry k distribuci zpráv mezi několik existujících předplatných tématu předvídatelným a vzájemně se vylučujícím způsobem. Vzor dělení se používá, když je systém škálován tak, aby zpracoval mnoho různých kontextů ve funkčně identických oddílech, které každý drží podmnožinu celkových dat; například informace o profilu zákazníka. S dělení, vydavatel odešle zprávu do tématu bez nutnosti jakékoli znalosti modelu dělení. Zpráva je pak přesunuta na správné předplatné, ze kterého pak lze načíst obslužnou rutinou zprávy oddílu.

Směrování používá filtry k distribuci zpráv mezi předplatnými témat předvídatelným způsobem, ale ne nutně výhradní. Ve spojení s funkcí [automatického předávání](service-bus-auto-forwarding.md) tématu filtry tématu lze použít k vytvoření složitých směrovacích grafů v rámci oboru názvů Service Bus pro distribuci zpráv v rámci oblasti Azure. Díky funkcím Azure nebo logicovým aplikacím Azure, které fungují jako most mezi obory názvů Azure Service Bus, můžete vytvářet složité globální topologie s přímou integrací do podnikových aplikací.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)