---
title: Úvod do spolehlivých kolekcí
description: Stavové služby Service Fabric poskytují spolehlivé kolekce, které umožňují psát vysoce dostupné, škálovatelné a nízkolatencové cloudové aplikace.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 78ecc57a4da43bf416839226253e6d0e2f4c1651
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398436"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Úvod do spolehlivé kolekce ve stavových službách Azure Service Fabric

Spolehlivé kolekce umožňují psát vysoce dostupné, škálovatelné a nízkolatencové cloudové aplikace, jako byste psali aplikace pro jednu počítač. Třídy v oboru názvů **Microsoft.ServiceFabric.Data.Collections** poskytují sadu kolekcí, které automaticky zpřístupňují váš stav. Vývojáři potřebují programovat pouze na rozhraní API spolehlivé kolekce a nechat spolehlivé kolekce spravovat replikované a místní stav.

Hlavní rozdíl mezi spolehlivé kolekce a další chudou technologie (například Redis, Azure Table service a Azure Queue service) je, že stav je udržována místně v instanci služby a zároveň jsou vysoce dostupné. To znamená, že:

* Všechna čtení jsou místní, což má za následek nízkou latenci a čtení s vysokou propustností.
* Všem zápisům vzniká minimální počet síťových vi, což má za následek nízkou latenci a zápisy s vysokou propustností.

![Obrázek vývoje sbírek.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Spolehlivé kolekce si lze myslet jako přirozený vývoj **System.Collections třídy:** nová sada kolekcí, které jsou určeny pro cloud a multi-počítačové aplikace bez zvýšení složitosti pro vývojáře. Jako takové jsou spolehlivé kolekce:

* Replikováno: Změny stavu jsou replikovány pro vysokou dostupnost.
* Asynchronní: Rozhraní API jsou asynchronní, aby bylo zajištěno, že vlákna nejsou blokovány při vzniku vi.
* Transakční: API využívají abstrakci transakcí, takže můžete snadno spravovat více spolehlivé kolekce v rámci služby.
* Trvalé nebo nestálé: Data mohou být uchovány na disk pro odolnost proti výpadky ve velkém měřítku (například výpadku napájení datového centra). Některé spolehlivé kolekce také podporují nestálý režim (s [upozorněními),](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)kde jsou všechna data uložena v paměti, například replikovaná mezipaměť v paměti.

Spolehlivé kolekce poskytují silné záruky konzistence po vybalení z krabice, aby se usnadnilo uvažování o stavu aplikace.
Silné konzistence je dosaženo zajištěním potvrzení transakce dokončit až po celou transakci byla zaznamenána na většinové kvorum replik, včetně primární.
Chcete-li dosáhnout slabší konzistence, aplikace mohou potvrdit zpět klientovi nebo žadateli před vrácením asynchronního potvrzení.

Spolehlivé kolekce API jsou vývoj souběžných kolekcí API (nalezený v **System.Collections.Concurrent** obor názvů):

* Asynchronní: Vrátí úlohu, protože na rozdíl od souběžných kolekcí jsou operace replikovány a trvalé.
* Žádné out parametry: `ConditionalValue<T>` Používá `bool` se k vrácení a a hodnota namísto out parametry. `ConditionalValue<T>`je `Nullable<T>` jako, ale nevyžaduje T být struct.
* Transakce: Používá objekt transakce, který umožňuje uživateli seskupit akce na více spolehlivých kolekcí v transakci.

**Microsoft.ServiceFabric.Data.Collections** dnes obsahuje tři kolekce:

* [Spolehlivý slovník](https://msdn.microsoft.com/library/azure/dn971511.aspx): Představuje replikované, transakční a asynchronní kolekce párů klíč/hodnota. Podobně jako **ConcurrentDictionary**, klíč i hodnota může být libovolného typu.
* [Spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx): Představuje replikované, transakční a asynchronní přísné fronty fifo (first-in, first-out). Podobně jako **ConcurrentQueue**, hodnota může být libovolného typu.
* [Spolehlivá souběžná fronta](service-fabric-reliable-services-reliable-concurrent-queue.md): Představuje replikované, transakční a asynchronní nejlepší úsilí řazení fronty pro vysokou propustnost. Podobně jako **ConcurrentQueue**, hodnota může být libovolného typu.

## <a name="next-steps"></a>Další kroky

* [Doporučení pro & spolehlivé kolekce](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Spolehlivá serializace kolekcí](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Spolehlivá konfigurace správce stavu](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Rychlý start spolehlivých služeb](service-fabric-reliable-services-quick-start.md)
  * [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
