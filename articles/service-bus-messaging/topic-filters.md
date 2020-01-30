---
title: Azure Service Bus filtry témat | Microsoft Docs
description: Tento článek vysvětluje, jak můžou předplatitelé definovat, které zprávy chce přijímat z tématu zadáním filtrů.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774495"
---
# <a name="topic-filters-and-actions"></a>Akce a filtry témat

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy jsou určeny ve formě jednoho nebo více pojmenovaných pravidel předplatného. Každé pravidlo se skládá z podmínky, která vybere konkrétní zprávy a akci, která vydává poznámku vybrané zprávě. Pro každou odpovídající podmínku pravidla odběr vytvoří kopii zprávy, která může pro každé odpovídající pravidlo obsahovat jiné poznámky.

Každé nově vytvořené předplatné tématu má počáteční výchozí pravidlo předplatného. Pokud explicitně neurčíte podmínku filtru pro pravidlo, je použit filtr na **true** , který umožňuje výběr všech zpráv v rámci předplatného. K výchozímu pravidlu není přidružena žádná akce anotace.

Service Bus podporuje tři podmínky filtru:

-   *Logické filtry* – **TrueFilter** a **FalseFilter** buď způsobí, že se pro odběr vybraly všechny přicházející zprávy (**true**) nebo žádná z doručených zpráv (**false**).

-   *Filtry SQL* – **SqlFilter** obsahuje podmíněný výraz podobný SQL, který se vyhodnocuje ve zprostředkovateli proti uživatelsky definovaným vlastnostem a vlastnostem systému. Všechny vlastnosti systému musí být v podmíněných výrazech předponou `sys.`. [Podsada SQL-Language pro testy podmínek filtrování](service-bus-messaging-sql-filter.md) pro existenci vlastností (`EXISTS`) a také pro hodnoty null (`IS NULL`), logické operátory not/a/nebo, relační operátory, jednoduché číselné aritmetické operace a jednoduché textové vzorce se shodují s `LIKE`.

-   *Filtry korelace* – **CorrelationFilter** obsahuje sadu podmínek, které se shodují s jednou nebo více uživateli a vlastnostmi systému, které přicházejí. Běžné použití se shoduje s vlastností **ID korelace** , ale aplikace může také zvolit, aby odpovídala hodnotám **ContentType**, **Label**, **MessageID**, **ReplyTo**, **ReplyToSessionId**, **SessionID** **, a a jakýmkoli**uživatelsky definovaným vlastnostem. Shoda existuje, pokud je hodnota doručené zprávy pro vlastnost rovna hodnotě zadané ve filtru korelace. U řetězcových výrazů porovnávání rozlišuje velká a malá písmena. Pokud zadáte více vlastností shody, filtr je zkombinuje jako logický operátor AND, což znamená, že aby se filtr shodoval, všechny podmínky se musí shodovat.

Všechny filtry vyhodnocují vlastnosti zprávy. Filtry nemohou vyhodnotit tělo zprávy.

Složitá pravidla filtru vyžadují kapacitu zpracování. Konkrétně použití pravidel filtru SQL vede ke snížení celkové propustnosti zpráv na úrovni předplatného, tématu a oboru názvů. Kdykoli je to možné, aplikace by měly zvolit filtry korelace přes filtry podobné SQL, protože jsou mnohem efektivnější při zpracování, takže mají méně vliv na propustnost.

## <a name="actions"></a>Akce

Pomocí podmínek filtru SQL můžete definovat akci, která může opatřit poznámky zprávou přidáním, odebráním nebo nahrazením vlastností a jejich hodnot. Akce [používá výraz podobný jazyku SQL](service-bus-messaging-sql-filter.md) , který je volně vylibové na SYNTAXI příkazu SQL Update. Tato akce se provádí ve zprávě poté, co byla shodná, a předtím, než je zpráva vybrána v rámci předplatného. Změny vlastností zprávy jsou pro zprávu zkopírovanou do předplatného soukromé.

## <a name="usage-patterns"></a>Vzory využití

Nejjednodušším scénářem použití pro téma je to, že každé předplatné získá kopii každé zprávy odeslané do tématu, které povoluje vzor všesměrového vysílání.

Filtry a akce umožňují dvě další skupiny vzorů: vytváření oddílů a směrování.

Vytváření oddílů používá filtry k distribuci zpráv v několika stávajících předplatných tématu, a to předvídatelným a vzájemně se vylučujícím způsobem. Vzor dělení se používá, když je systém rozložen na kapacitu pro zpracování mnoha různých kontextů v funkčně identických oddílech, které každý drží podmnožinu celkových dat; například informace o profilu zákazníka. Při vytváření oddílů odesílá Vydavatel zprávu do tématu bez nutnosti znát model dělení. Zpráva se pak přesune do správného předplatného, ze kterého ji pak může načíst obslužná rutina zprávy oddílu.

Směrování používá filtry k distribuci zpráv mezi předplatnými tématu, ale ne nutně exkluzivní. Ve spojení s funkcí [automatického předávání](service-bus-auto-forwarding.md) je možné pomocí filtrů v rámci oboru názvů Service Bus v rámci oblasti Azure vytvořit komplexní grafy směrování v rámci oboru názvů pro distribuci zpráv. Když Azure Functions nebo Azure Logic Apps funguje jako most mezi Azure Service Bus obory názvů, můžete vytvořit komplexní globální topologie s přímou integrací do obchodních aplikací.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)