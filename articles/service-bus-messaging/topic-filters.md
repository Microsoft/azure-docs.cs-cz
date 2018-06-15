---
title: Filtry tématu Azure Service Bus | Microsoft Docs
description: Filtrovat témata Azure Service Bus
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 305c017bd49f233c10479e2c33ec8db72cae3aa7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198864"
---
# <a name="topic-filters-and-actions"></a>Téma filtry a akce

Odběratelé můžete definovat zprávy, které chtějí dostávat téma. Tyto zprávy jsou uvedeny ve formě jednoho nebo více pravidel s názvem odběru. Každé pravidlo obsahuje podmínku, která vybere konkrétní zprávy a akce, která označí vybrané zprávy. Pro každý odpovídající pravidlo podmínku vytvoří odběr kopie zprávy, která může být jinak poznámky. pro každý odpovídající pravidlo.

Každé předplatné nově vytvořené téma má pravidlo počáteční výchozí předplatné. Pokud nezadáte explicitně podmínku filtrování pro pravidlo, je použitý filtr **true** filtr, který umožňuje všechny zprávy, aby byl vybrán do předplatného. Výchozí pravidlo nemá žádnou akci přidružené poznámky.

Service Bus podporuje tři filtr podmínek:

-   *Logická hodnota filtry* – **TrueFilter** a **FalseFilter** buď způsobit, že všechny nově příchozí zprávy (**true**), nebo žádná které zprávy (**false**) Chcete-li být vybraná pro odběr.

-   *Filtry SQL* – **SqlFilter** obsahuje podobné jazyku SQL podmíněným výrazem, který se vyhodnotí v zprostředkovatele před uživatelem definované vlastnosti a vlastnosti systému příchozí zprávy. Všechny vlastnosti systému musí obsahovat předponu `sys.` podmíněného výrazu. [Podmnožina jazyk SQL pro filtr podmínek](service-bus-messaging-sql-filter.md) testy existence vlastnosti (EXISTS), stejně jako-hodnoty null (není NULL), logické není/AND nebo OR, relační operátory, jednoduché aritmetické číselné a jednoduchý text vzor odpovídající pomocí jako.

-   *Korelace filtry* – **CorrelationFilter** obsahuje sadu podmínek, které se shodují na jeden nebo více vlastností příchozích zpráv uživatele a systémové. Se běžně používá k porovnání **CorrelationId** vlastnost, ale aplikace můžete také zvolit odpovídající **ContentType**, **popisek**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **k**a všechny uživatelem definované Vlastnosti. Odpovídající položka existuje po rovna hodnotě zadaných ve filtru korelace které zprávu hodnotu vlastnosti. Řetězec výrazy porovnání je malá a velká písmena. Při zadávání více vlastností shoda, musí odpovídat všechny podmínky, filtr je kombinuje jako logické a podmínku, což znamená pro filtr tak, aby odpovídaly.

Všechny filtry vyhodnocení vlastnosti zprávy. Filtry nelze vyhodnotit tělo zprávy.

Pravidla složitějších filtrů vyžadují kapacity zpracování. Použití pravidla filtru SQL na konkrétní výsledkem nižší celkovou propustnost zpráv na úrovni předplatného, tématu a obor názvů. Kdykoli je to možné, aplikace by měl vybrat korelace filtry přes SQL jako filtry, vzhledem k tomu, že jsou mnohem efektivnější ve zpracování a proto mít menší dopad na propustnost.

## <a name="actions"></a>Akce

S podmínkami filtru SQL a pouze s těmi můžete definovat akci, která může opatřit poznámkami zprávu pomocí přidání, odebrání nebo výměna vlastností a jejich hodnot. Akce [používá výraz SQL jako](service-bus-messaging-sql-filter.md) , volně leans na syntaxe příkazu aktualizace SQL. Akce se provádí na zprávu po mu odpovídala a předtím, než je vybrané zprávy do tématu. Změny vlastnosti zprávy jsou privátní na zprávu zkopírují do předplatného.

## <a name="usage-patterns"></a>Vzorce používání

Nejjednodušším scénáři využití pro téma je, že každé předplatné získá kopii každá zpráva odeslaná do tématu, které umožňuje všesměrového vysílání vzor.

Filtry a akce Povolit dva další skupiny schémat: vytváření oddílů a směrování.

Vytváření oddílů používá filtry distribuci zprávy přes několik existující odběry témat předvídatelný a vzájemně se vylučuje způsobem. Rozdělení vzor se používá, pokud je systém škálovat na více systémů pro zpracování mnoho různých kontextů v funkčně identické přihrádky, že každý obsahovat podmnožinu celkového dat; například zákazníka informace o profilu. Vydavatel s oddíly, odešle zprávu do tématu bez nutnosti znalostí dělení modelu. Zpráva je pak přesunuta do správné předplatné, ze kterého se dá pak načíst oddílu zpráva obslužnou rutinou.

Směrování používá filtry distribuci zprávy přes odběry témat předvídatelný způsobem, ale nemusí být výhradní. Ve spojení s [automatické přesměrování](service-bus-auto-forwarding.md) funkce, grafech tématu filtry lze použít k vytvoření komplexní směrování v rámci oboru názvů Service Bus pro distribuci zprávy v oblasti Azure. Pomocí funkce Azure nebo Azure Logic Apps, který funguje jako most mezi Azure Service Bus obory názvů můžete vytvářet složité globální topologie s přímá integrace do obchodních aplikací.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)