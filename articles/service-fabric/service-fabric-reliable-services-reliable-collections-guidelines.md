---
title: Pokyny a doporučení pro spolehlivé kolekce v Azure Service Fabric | Dokumentace Microsoftu
description: Pokyny a doporučení pro používání Service Fabric Reliable Collections
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: twhitney
ms.openlocfilehash: d50fee06a291e11898de19fd49bd657d2e1a6d00
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184929"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Pokyny a doporučení pro spolehlivé kolekce v Azure Service Fabric
Tato část obsahuje pokyny pro použití Reliable State Manager a Reliable Collections. Cílem je pomoct uživatelům se vyhnout běžným nástrahám.

Pokyny jsou uspořádané jako jednoduchý doporučení předponu podmínky *proveďte*, *zvažte*, *nepoužívejte* a *nepodporují*.

* Neprovádějte žádné změny objektu typu vlastní vrácený operace čtení (například `TryPeekAsync` nebo `TryGetValueAsync`). Reliable Collections, stejně jako souběžných kolekcí, vrátí odkaz na objekty a ne kopie.
* Před změnou ho proveďte hluboká kopie vráceného objektu vlastního typu. Protože vestavěné typy a struktury jsou pass podle hodnoty, není potřeba provést hluboká kopie na nich, pokud obsahují zadaný odkaz na pole nebo vlastnosti, které máte v úmyslu změnit.
* Nepoužívejte `TimeSpan.MaxValue` časových limitů. Časové limity by měla sloužit k zjišťování případů zablokování.
* Nepoužívejte transakce po byla potvrzena, byla přerušena nebo odstraněn.
* Nepoužívejte výčtu mimo obor transakce, který byl vytvořen v.
* Nevytvářejte transakce v rámci jiné transakce `using` příkaz vzhledem k tomu může dojít k zablokování.
* Ujistěte se, že vaše `IComparable<TKey>` implementace je správná. Systém má závislost `IComparable<TKey>` pro sloučení kontrolních bodů a řádků.
* Pomocí aktualizační zámek při čtení položky s záměr aktualizujte ji, aby se zabránilo třídu zablokování.
* Vezměte v úvahu uchování počet Reliable Collections na oddíl bude menší než 1 000. Preferovat Reliable Collections přes více spolehlivých kolekcí s menším počtem položek s více položek.
* Vezměte v úvahu udržování vašich položek (například TKey + TValue spolehlivého slovníku) nižší než 80 kB: menší tím lépe. To snižuje množství vstupně-VÝSTUPNÍCH požadavků haldy pro velké objekty využití stejně jako disku a sítě. Často snižuje replikovat duplicitních dat, když se aktualizuje jenom jednu malou část hodnoty. Běžný způsob, jak toho dosáhnout spolehlivého slovníku, je možnost proniknout řádků do více řádků.
* Zvažte použití zálohování a obnovení funkce, které mají zotavení po havárii.
* Nekombinujte ani operace jednu entitu a víc entitami operace (například `GetCountAsync`, `CreateEnumerableAsync`) v rámci jedné transakce z důvodu úrovních různé izolace.
* Zpracování InvalidOperationException. Uživatelské transakce můžete být přerušen systémem a různých důvodů. Například když Reliable State Manager mění svou roli z primární nebo když dlouhodobou transakci blokuje zkrácení protokolu transakcí. V takovém případě uživatel obdržet InvalidOperationException označující, že jejich transakce již byla ukončena. Za předpokladu, ukončení transakce nebyla požadavku uživatele, je nejlepší způsob, jak zpracovat tuto výjimku k uvolnění transakce, zkontrolujte, jestli byl signalizován token zrušení (nebo se změnila role repliky) a pokud ne vytvořit nové transakce a zkuste to znovu.  

Tady jsou některé možnosti brát v úvahu:

* Výchozí časový limit je čtyři sekundy pro všechna spolehlivé kolekce rozhraní API. Většinu uživatelů by měla používat výchozí časový limit.
* Je výchozí token zrušení `CancellationToken.None` v všechna rozhraní API spolehlivých kolekcí.
* Parametr typu klíče (*TKey*) pro spolehlivého slovníku musí správně implementovat `GetHashCode()` a `Equals()`. Klíče musí být neměnitelný.
* Abyste dosáhli vysoké dostupnosti pro spolehlivé kolekce, každá služba má nejméně jednoho cíle a minimální repliky nastavení velikosti 3.
* Operace čtení na sekundární může načíst verze, které nejsou potvrzeny kvora.
  To znamená, že verzi dat, která je pro čtení z jednoho sekundárního může být false provedení určité.
  Čtení z primárního jsou vždy stabilní: může nikdy být false provedení určité.
* Zabezpečení a ochrany osobních údajů data zachována podle rozhodnutí a předmět poskytovanou správu úložiště; je vaše aplikace ve spolehlivé kolekci TO ZNAMENÁ Šifrování disku operačního systému může použít k ochraně vašich dat v klidovém stavu.  

### <a name="next-steps"></a>Další postup
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Serializace a Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurace Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
