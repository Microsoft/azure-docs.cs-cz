---
title: Úvod do spolehlivých kolekcí ve stavových službách Azure Service Fabric | Microsoft Docs
description: Service Fabric stavové služby poskytují spolehlivé kolekce, které umožňují psát vysoce dostupné a škálovatelné cloudové aplikace a s nízkou latencí.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: atsenthi
ms.openlocfilehash: a7b30003fd02f8ab2e367311cdb3f56c80dbb4b2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599278"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Úvod do spolehlivých kolekcí ve stavových službách Azure Service Fabric

Spolehlivé kolekce vám umožňují psát vysoce dostupné, škálovatelné a nízké latence cloudových aplikací, jako byste psali jednotlivé počítačové aplikace. Třídy v oboru názvů **Microsoft. ServiceFabric. data. Collections** poskytují sadu kolekcí, které automaticky nastaví vysoce dostupný stav. Vývojáři potřebují programovat jenom pro spolehlivá rozhraní API kolekcí a spravovat replikované a místní stavy prostřednictvím spolehlivých kolekcí.

Klíčový rozdíl mezi spolehlivými kolekcemi a dalšími technologiemi s vysokou dostupností (například Redis, Azure Table service a Azure Služba front) spočívá v tom, že stav je uložen místně v instanci služby a zároveň je vysoce dostupný. To znamená, že:

* Všechna čtení jsou místní, což vede ke čtení s nízkou latencí a vysokou propustností.
* Všechna zápisy vycházejí z minimálního počtu síťových IOs, což vede k zápisu s nízkou latencí a vysokou propustností.

![Obrázek vývoje kolekcí](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Spolehlivé kolekce si můžete představit jako přirozený vývoj tříd třídy **System. Collections** : novou sadu kolekcí, které jsou navržené pro cloudové a multi-počítačové aplikace, aniž by bylo potřeba zvyšovat složitost vývojářů. V takovém případě jsou spolehlivé kolekce:

* Replikovateln Změny stavu se replikují pro zajištění vysoké dostupnosti.
* Trvalé Data jsou trvalá na disku pro zajištění odolnosti proti výpadkům ve velkém rozsahu (například výpadek napájení datového centra).
* Vzhledem k tomu, že zápisy jsou trvalé a replikované, nemůžete vytvořit nestálou ReliableDictionary, ReliableQueue nebo jinou spolehlivou kolekci, která pouze uchovává data v paměti.
* Asynchronně Rozhraní API jsou asynchronní, aby se zajistilo, že při vzniku vstupně-výstupních operací nejsou blokována vlákna
* Doručen Rozhraní API využívají abstrakci transakcí, takže můžete snadno spravovat více spolehlivých kolekcí v rámci služby.

Spolehlivé kolekce poskytují silné záruky konzistence vycházející ze seznamu, aby bylo možné důvodně zlepšit stav aplikace.
Silná konzistence se dosahuje tím, že zajišťují, že potvrzení transakcí se dokončí až po zaznamenání celé transakce na většinu kvora replik, včetně primární.
Aby se zajistila slabší konzistence, můžou aplikace před vrácením asynchronního potvrzení vrátit klientovi/žadateli.

Spolehlivé kolekce rozhraní API jsou vývoj souběžných kolekcí rozhraní API (najdete je v oboru názvů **System. Collections.** a souběžně):

* Asynchronně Vrátí úlohu, protože na rozdíl od souběžných kolekcí je operace replikovaná a trvalá.
* Žádné výstupní parametry: Používá `ConditionalValue<T>` k`bool` vrácení a hodnoty namísto parametrů out. `ConditionalValue<T>`je jako `Nullable<T>` , ale nevyžaduje T jako strukturu.
* Převody Používá objekt transakce k tomu, aby uživatel mohl seskupit akce v několika spolehlivých kolekcích v transakci.

Dnes, **Microsoft. ServiceFabric. data. Collections** obsahuje tři kolekce:

* [Spolehlivý slovník](https://msdn.microsoft.com/library/azure/dn971511.aspx): Představuje replikovanou, transakční a asynchronní kolekci párů klíč/hodnota. Podobně jako **ConcurrentDictionary**, klíč i hodnota mohou být libovolného typu.
* [Spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx): Představuje replikovanou, transakční a asynchronní metodu first-in, First-out (FIFO) Queue. Podobně jako **ConcurrentQueue**, hodnota může být libovolného typu.
* [Spolehlivá souběžná fronta](service-fabric-reliable-services-reliable-concurrent-queue.md): Představuje replikovanou, transakční a asynchronní frontu pro řazení osvědčených úkonů pro vysokou propustnost. Podobně jako u **ConcurrentQueue**může být hodnota libovolného typu.

## <a name="next-steps"></a>Další postup

* [Pokyny pro spolehlivé kolekce & doporučení](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Spolehlivá serializace kolekcí](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurace spolehlivého správce stavu](service-fabric-reliable-services-configuration.md)
* Jiné
  * [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
