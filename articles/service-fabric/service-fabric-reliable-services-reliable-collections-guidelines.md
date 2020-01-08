---
title: Pokyny pro spolehlivé kolekce
description: Pokyny a doporučení pro použití Service Fabric spolehlivých kolekcí v aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/10/2017
ms.openlocfilehash: 37c734205877f9e0cb98ef2834462691e8e483d9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645476"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Pokyny a doporučení pro spolehlivé kolekce v Azure Service Fabric
V této části najdete pokyny pro použití spolehlivého správce stavu a spolehlivých kolekcí. Cílem je pomáhat uživatelům vyhnout se běžným nástrah.

Pokyny jsou uspořádané jako jednoduchá doporučení s předponou, zvažte *,* jestli *nechcete a nechcete*.

* Neupravujte objekt vlastního typu vráceného operacemi čtení (například `TryPeekAsync` nebo `TryGetValueAsync`). Spolehlivé kolekce, stejně jako souběžné kolekce, vrátí odkaz na objekty a nikoli kopii.
* Před úpravou přepište vrácený objekt vlastního typu. Vzhledem k tomu, že struktury a předdefinované typy jsou předávány, nemusíte provádět hloubkovou kopii, pokud neobsahují pole typu odkazu nebo vlastnosti, které chcete upravit.
* Nepoužívejte `TimeSpan.MaxValue` pro časové limity. Pro detekci zablokování by se měly použít časové limity.
* Nepoužívejte transakci poté, co byla potvrzena, přerušena nebo vyřazena.
* Nepoužívejte výčet mimo obor transakce, ve kterém byl vytvořen.
* Nevytvářejte transakci v rámci příkazu `using` jiné transakce, protože může způsobit zablokování.
* Nevytvářejte spolehlivý stav s `IReliableStateManager.GetOrAddAsync` a používejte spolehlivý stav ve stejné transakci. Výsledkem je InvalidOperationException.
* Ujistěte se, že je vaše implementace `IComparable<TKey>` správná. Systém při slučování kontrolních bodů a řádků využívá závislost na `IComparable<TKey>`.
* Použijte při čtení položky s úmyslem aktualizovat zámek, aby se zabránilo určité třídě zablokování.
* Zvažte udržení počtu spolehlivých kolekcí na oddíl, které jsou menší než 1000. Preferovat spolehlivé kolekce s více položkami přes více spolehlivých kolekcí s menším počtem položek.
* Zvažte zachování svých položek (například TKey + TValue pro Reliable Dictionary) pod 80 KB: menší než vyšší. Tím se sníží množství Large Object využití haldy i vstupně-výstupní požadavky na disk a síť. V případě, že se aktualizuje jenom jedna malá část hodnoty, se často zkracuje replikace duplicitních dat. Běžným způsobem, jak toho dosáhnout ve spolehlivém slovníku, je rozdělit řádky do více řádků.
* Zvažte použití funkcí zálohování a obnovení k zotavení po havárii.
* Vyhněte se kombinování operací s jednou entitou a operací více entit (například `GetCountAsync`, `CreateEnumerableAsync`) ve stejné transakci z důvodu různých úrovní izolace.
* Zpracujte InvalidOperationException. Uživatelské transakce může systém přerušit z nejrůznějších důvodů. Například když správce spolehlivého stavu mění svou roli mimo primární nebo když dlouho běžící transakce blokuje zkrácení transakčního protokolu. V takových případech může uživatel obdržet příkaz InvalidOperationException oznamující, že jejich transakce již byla ukončena. Za předpokladu, že ukončení transakce nebylo vyžádáno uživatelem, nejlepším způsobem, jak zpracovat tuto výjimku, je uvolnit transakci, ověřit, zda byl token zrušení signál (nebo aby se změnila role repliky), a pokud není vytvořen nový transakce a zkuste to znovu.  

Tady je několik věcí, které je potřeba vzít v úvahu:

* Výchozí časový limit je čtyři sekundy pro všechna rozhraní Reliable Collection API. Většina uživatelů by měla používat výchozí časový limit.
* Výchozí token zrušení je `CancellationToken.None` ve všech rozhraních API Reliable Collections.
* Parametr typu klíče (*TKey*) pro spolehlivý slovník musí správně implementovat `GetHashCode()` a `Equals()`. Klíče musí být neměnné.
* Aby bylo možné dosáhnout vysoké dostupnosti pro spolehlivé kolekce, musí mít každá služba aspoň cílovou a minimální velikost sady replik 3.
* Operace čtení sekundárního nástroje můžou číst verze, které nejsou potvrzené jako kvorum.
  To znamená, že ve verzi dat, která je čtena z jednoho sekundárního, může být nepravdivá.
  Čtení z primární jsou vždycky stabilní: nedají se na něj nikdy zabývat.
* Zabezpečení/Ochrana osobních údajů pro data trvalá vaší aplikací ve spolehlivé kolekci je vaším rozhodnutím a podléhá ochranám, které poskytuje vaše Správa úložiště; t. K ochraně vašich dat v klidovém režimu se dá použít šifrování disku s operačním systémem.  

### <a name="next-steps"></a>Další kroky
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurace spolehlivého správce stavu](service-fabric-reliable-services-configuration.md)
* Další
  * [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
