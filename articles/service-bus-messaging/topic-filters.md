---
title: Azure Service Bus filtry témat | Microsoft Docs
description: Tento článek vysvětluje, jak můžou předplatitelé definovat, které zprávy chce přijímat z tématu zadáním filtrů.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5df343ff63c01a7cf10315b758e3d6fba8ac5674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066742"
---
# <a name="topic-filters-and-actions"></a>Akce a filtry témat

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy se určují ve formě jednoho nebo více pojmenovaných pravidel odběru. Každé pravidlo se skládá z podmínky, která vybere konkrétní zprávy a akci, která vydává poznámku vybrané zprávě. Pro každou odpovídající podmínku pravidla odběr vytvoří kopii zprávy, která může pro každé odpovídající pravidlo obsahovat jiné poznámky.

Každé nově vytvořené předplatné tématu má počáteční výchozí pravidlo předplatného. Pokud explicitně neurčíte podmínku filtru pro pravidlo, je použit filtr na **true** , který umožňuje výběr všech zpráv v rámci předplatného. K výchozímu pravidlu není přidružena žádná akce anotace.

Service Bus podporuje tři podmínky filtru:

-   *Logické filtry* – **TrueFilter** a **FalseFilter** buď způsobí, že se pro odběr vybraly všechny přicházející zprávy (**true**) nebo žádná z doručených zpráv (**false**).

-   *Filtry SQL* – **SqlFilter** obsahuje podmíněný výraz podobný SQL, který se vyhodnocuje ve zprostředkovateli proti uživatelsky definovaným vlastnostem a vlastnostem systému. Všechny vlastnosti systému musí být `sys.` v podmíněném výrazu předponou. [Podmnožina jazyka SQL pro podmínky filtru](service-bus-messaging-sql-filter.md) testy pro existenci vlastností ( `EXISTS` ), hodnot null ( `IS NULL` ), logických operátorů not/a/nebo, relačních operátorů, jednoduchého číselného aritmetického zpracování a jednoduchých textových vzorů `LIKE` .

-   *Filtry korelace* – **CorrelationFilter** obsahuje sadu podmínek, které se shodují s jednou nebo více uživateli a vlastnostmi systému, které přicházejí. Běžné použití se shoduje s vlastností **ID korelace** , ale aplikace může také zvolit shodu s následujícími vlastnostmi:

    - **Třída**
     - **Popisek**
     - **Parametr**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Záměr**
     - jakékoli uživatelsky definované vlastnosti. 
     
     Shoda existuje, pokud je hodnota doručené zprávy pro vlastnost rovna hodnotě zadané ve filtru korelace. U řetězcových výrazů porovnávání rozlišuje velká a malá písmena. Pokud zadáte více vlastností shody, filtr je zkombinuje jako logický operátor AND, což znamená, že aby se filtr shodoval, všechny podmínky se musí shodovat.

Všechny filtry vyhodnocují vlastnosti zprávy. Filtry nemůžou vyhodnotit tělo zprávy.

Složitá pravidla filtru vyžadují kapacitu zpracování. Konkrétně použití pravidel filtru SQL způsobí snížení celkové propustnosti zpráv na úrovni předplatného, tématu a oboru názvů. Kdykoli je to možné, aplikace by měly zvolit filtry korelace přes filtry podobné SQL, protože jsou mnohem efektivnější ve zpracování a mají méně vliv na propustnost.

## <a name="actions"></a>Akce

Pomocí podmínek filtru SQL můžete definovat akci, která může opatřit poznámky zprávou přidáním, odebráním nebo nahrazením vlastností a jejich hodnot. Akce [používá výraz podobný jazyku SQL](service-bus-messaging-sql-filter.md) , který je volně vylibové na SYNTAXI příkazu SQL Update. Tato akce se provádí ve zprávě poté, co byla shodná a před tím, než je vybrána zpráva v rámci předplatného. Změny vlastností zprávy jsou pro zprávu zkopírovanou do předplatného soukromé.

## <a name="usage-patterns"></a>Vzory využití

Nejjednodušším scénářem použití pro téma je to, že každé předplatné získá kopii každé zprávy odeslané do tématu, které povoluje vzor všesměrového vysílání.

Filtry a akce umožňují dvě další skupiny vzorů: vytváření oddílů a směrování.

Vytváření oddílů používá filtry k distribuci zpráv v několika stávajících předplatných tématu, a to předvídatelným a vzájemně se vylučujícím způsobem. Vzor dělení se používá, když je systém rozložen na kapacitu pro zpracování mnoha různých kontextů v funkčně identických oddílech, které každý drží podmnožinu celkových dat; například informace o profilu zákazníka. Při vytváření oddílů odesílá Vydavatel zprávu do tématu bez nutnosti znát model dělení. Zpráva se pak přesune do správného předplatného, ze kterého ji pak může načíst obslužná rutina zprávy oddílu.

Směrování používá filtry k distribuci zpráv mezi předplatnými tématu, ale ne nutně exkluzivní. Ve spojení s funkcí [automatického předávání](service-bus-auto-forwarding.md) je možné pomocí filtrů v rámci oboru názvů Service Bus v rámci oblasti Azure vytvořit komplexní grafy směrování v rámci oboru názvů pro distribuci zpráv. Když Azure Functions nebo Azure Logic Apps funguje jako most mezi Azure Service Bus obory názvů, můžete vytvořit komplexní globální topologie s přímou integrací do obchodních aplikací.


> [!NOTE]
> Vzhledem k tomu, že Azure Portal nyní podporuje funkce Průzkumníka Service Bus, je možné vytvořit nebo upravit filtry předplatného na portálu. 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující ukázky: 

- [Kurz pro odesílání a přijímání v rámci .NET Basic s filtry](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – filtry témat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Šablona Azure Resource Manageru](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)