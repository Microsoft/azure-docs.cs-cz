---
title: Pokyny pro spolehlivé kolekce
description: Pokyny a doporučení pro použití service fabric spolehlivé kolekce v aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/10/2017
ms.openlocfilehash: 37c734205877f9e0cb98ef2834462691e8e483d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645476"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Pokyny a doporučení pro spolehlivé kolekce ve službě Azure Fabric
Tato část obsahuje pokyny pro použití spolehlivého správce stavu a spolehlivé kolekce. Cílem je pomoci uživatelům vyhnout se běžným nástrahám.

Pokyny jsou uspořádány jako jednoduchá doporučení s předponou s podmínkami *Do*, *Zvažte*, *Vyhnout* a *Ne.*

* Neupravujte objekt vlastního typu vrácený operacemi `TryPeekAsync` čtení `TryGetValueAsync`(například nebo ). Spolehlivé kolekce, stejně jako souběžné kolekce, vrátí odkaz na objekty a není kopie.
* Před úpravou hloubkové kopírování vráceného objektu vlastního typu proveďte hloubkovou kopii. Vzhledem k tomu, že struktury a předdefinované typy jsou pass-by-value, není nutné provést hluboké kopie na nich, pokud obsahují pole typu odkazu nebo vlastnosti, které chcete upravit.
* Nepoužívejte `TimeSpan.MaxValue` pro časové osy. Časové osy by měly být použity k detekci zablokování.
* Nepoužívejte transakci po její potvrzené, přerušené nebo vyřazené.
* Nepoužívejte výčet mimo rozsah transakce, ve které byl vytvořen.
* Nevytvářejte transakci v rámci `using` příkazu jiné transakce, protože může způsobit zablokování.
* Nevytvářejte spolehlivý `IReliableStateManager.GetOrAddAsync` stav se spolehlivým stavem a používejte jej ve stejné transakci. Výsledkem je InvalidOperationException.
* Ujistěte se, že vaše `IComparable<TKey>` implementace je správná. Systém přebírá závislost `IComparable<TKey>` na sloučení kontrolních bodů a řádků.
* Použijte update zámek při čtení položky s úmyslem aktualizovat, aby se zabránilo určité třídy zablokování.
* Zvažte zachování počet spolehlivé kolekce na oddíl být menší než 1000. Preferujte spolehlivé kolekce s více položkami před spolehlivějšími kolekcemi s menším počtem položek.
* Zvažte zachování položek (například TKey + TValue pro spolehlivý slovník) pod 80 kBytes: menší, tím lépe. To snižuje množství využití haldy velkých objektů, stejně jako požadavky na disk a síť vi. Často snižuje replikaci duplicitních dat při aktualizaci pouze jedné malé části hodnoty. Běžným způsobem, jak toho dosáhnout ve spolehlivém slovníku, je rozdělit řádky do více řádků.
* Zvažte použití funkce zálohování a obnovení k zotavení po havárii.
* Vyhněte se směšování operací jedné `GetCountAsync`entity `CreateEnumerableAsync`a operací s více subjekty (např. ) ve stejné transakci z důvodu různých úrovní izolace.
* Zpracovat InvalidOperationException. Transakce uživatele může být přerušena systémem z různých důvodů. Například když správce spolehlivého stavu mění svou roli z primární nebo když dlouhotrvající transakce blokuje zkrácení transakční protokol. V takových případech může uživatel obdržet InvalidOperationException označující, že jejich transakce již byla ukončena. Za předpokladu, že ukončení transakce nebylo požadováno uživatelem, nejlepší způsob, jak zpracovat tuto výjimku, je zcizit transakci, zkontrolovat, zda byl signalizován token zrušení (nebo byla změněna role repliky) a pokud není vytvořit nový transakce a opakování.  

Zde je několik věcí, které je třeba mít na paměti:

* Výchozí časový limit je čtyři sekundy pro všechna nastavení API spolehlivé kolekce. Většina uživatelů by měla používat výchozí časový limit.
* Výchozí token zrušení `CancellationToken.None` je ve všech ověřování api spolehlivé kolekce.
* Parametr typu klíče (*TKey*) pro spolehlivý `GetHashCode()` `Equals()`slovník musí správně implementovat a . Klíče musí být neměnné.
* Chcete-li dosáhnout vysoké dostupnosti pro spolehlivé kolekce, každá služba by měla mít alespoň cíl a minimální velikost sady replik 3.
* Operace čtení na sekundární může číst verze, které nejsou kvorum potvrzena.
  To znamená, že verze dat, která je čtena z jednoho sekundárního může být false postupoval.
  Čtení z Primární jsou vždy stabilní: nikdy nemůže být false postupoval.
* Zabezpečení/ochrana osobních údajů utrchvaných vaší aplikací ve spolehlivém shromažďování je vaše rozhodnutí a podléhá ochraně poskytované vaší správou úložiště; Tj. Šifrování disku operačního systému lze použít k ochraně dat v klidovém stavu.  

### <a name="next-steps"></a>Další kroky
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Spolehlivá konfigurace správce stavu](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Spolehlivý rychlý start služeb](service-fabric-reliable-services-quick-start.md)
  * [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
