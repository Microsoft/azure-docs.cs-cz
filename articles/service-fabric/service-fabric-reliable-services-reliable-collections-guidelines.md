---
title: "Pokyny a doporučení pro spolehlivé kolekce v Azure Service Fabric | Microsoft Docs"
description: "Pokyny a doporučení pro používání služby Fabric spolehlivé kolekce"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: 27ea71bcc378100e613a8edd1c57a93f3c9ed925
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Pokyny a doporučení pro spolehlivé kolekce v Azure Service Fabric
Tato část obsahuje pokyny pro použití spolehlivé správce stavu a spolehlivé kolekce. Cílem je pomoct uživatelům vyhnout se běžné nástrahy.

Pokyny jsou uspořádána jako jednoduchý doporučení předponu s podmínkami *provést*, *zvažte*, *nepoužívejte* a *nepodporují*.

* Nelze změnit objekt typu vlastní vrácené operací čtení (například `TryPeekAsync` nebo `TryGetValueAsync`). Spolehlivé kolekcí, stejně jako souběžných kolekcí, vrátí odkaz na objekty a nelze zkopírovat.
* Proveďte hloubkové kopie vráceného objektu vlastního typu před změnou ho. Vzhledem k tomu, že struktury a vestavěné typy jsou průchodu hodnotu, není nutné provést hloubkové kopie na ně, dokud obsahují odkaz na zadané pole nebo vlastnosti, které chcete upravit.
* Nepoužívejte `TimeSpan.MaxValue` časových limitů. Vypršení časových limitů se má použít k detekci zablokování.
* Nepoužívejte transakce po bylo potvrzeno, byl zrušen nebo zlikvidován.
* Nepoužívejte výčet mimo, který byl vytvořen v oboru transakce.
* Nevytvářejte transakce v rámci jiné transakci `using` příkaz vzhledem k tomu může dojít k zablokování.
* Ujistěte se, že vaše `IComparable<TKey>` implementace je správný. Systém má závislost `IComparable<TKey>` ke sloučení kontrolních bodů a řádky.
* Použití aktualizační zámek při čtení položky s záměr aktualizujte, aby se zabránilo třídu zablokování.
* Vezměte v úvahu zachování počet kolekcí, spolehlivé na oddíl být menší než 1 000. Upřednostnit spolehlivé kolekce s více položkami přes více spolehlivé kolekcí s menším počtem položek.
* Zvažte zachování položek (například TKey + TValue pro spolehlivé slovník) nižší než 80 kB: menší tím lépe. Tím se snižuje množství velkého objektu haldy využití a také diskových a síťových vstupně-VÝSTUPNÍMI požadavky. Často snižuje replikace duplicitních dat, je-li aktualizován pouze jeden malou část hodnoty. Běžným způsobem to můžete udělat ve slovníku spolehlivé, je proniknout vaší řádky do více řádků.
* Zvažte použití zálohování a obnovení funkce tak, aby měl zotavení po havárii.
* Vyhněte se kombinování jedné entity operací a operací s více entit (např `GetCountAsync`, `CreateEnumerableAsync`) v rámci jedné transakce z důvodu izolace různé úrovně.
* Zpracování výjimkou InvalidOperationException. Uživatelské transakce může být přerušil systému různých důvodů. Například když spolehlivé správce stavu mění jeho roli z primární nebo když dlouhotrvající transakci blokuje zkrácení protokolu transakcí. V takových případech uživatel obdržet InvalidOperationException, která určuje, že jejich transakce již byla ukončena. Za předpokladu, ukončení transakce nebyla požadované uživatelem, je nejlepší způsob, jak zpracovat výjimku k uvolnění transakce, zkontrolujte, jestli nesignalizuje token zrušení (nebo se změnily role repliky) a pokud není vytvořit novou transakci a zkuste to znovu.  

Zde jsou některé věci, třeba vzít v úvahu:

* Výchozí časový limit je čtyři sekund pro všechny spolehlivé kolekce rozhraní API. Většina uživatelů měli používat výchozí časový limit.
* Výchozí token zrušení je `CancellationToken.None` v všechna rozhraní API spolehlivé kolekce.
* Parametr typ klíče (*TKey*) pro slovník spolehlivé musí správně implementovat `GetHashCode()` a `Equals()`. Klíče musí být neměnitelný.
* K dosažení vysoké dostupnosti pro spolehlivé kolekce, každá služba měli aspoň cíle a minimální repliky nastavit velikost 3.
* Operace čtení na sekundárním lze číst verze, které nejsou kvora potvrzené.
  To znamená, že může být verzi data, která je pro čtení z jedné sekundární false zvýšily.
  Čtení z primární jsou vždycky stabilní: můžete nikdy být false zvýšily.

### <a name="next-steps"></a>Další postup
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky.](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [Správce spolehlivé stavu a interní informace o kolekci](service-fabric-reliable-services-reliable-collections-internals.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Serializace a Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuration Manager spolehlivé stavu](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
