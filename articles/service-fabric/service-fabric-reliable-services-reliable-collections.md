---
title: Úvod do spolehlivé kolekcí v stavové služby Azure Service Fabric | Microsoft Docs
description: Service Fabric stavové služby poskytují spolehlivé kolekce, které vám umožní zápisu vysoce dostupných, škálovatelných a nízkou latencí cloudové aplikace.
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
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: c6425f7642d9dbecafc6779184a2a5b0cc1a2cab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Úvod do spolehlivé kolekcí v stavové služby Azure Service Fabric
Spolehlivé kolekce umožňují zápisu vysoce dostupných, škálovatelných a nízkou latencí cloudové aplikace, jako kdyby byly psaní aplikací jeden počítač. Třídy v **Microsoft.ServiceFabric.Data.Collections** obor názvů poskytují sadu kolekcí, které automaticky nastavit vašemu stavu jako vysoce dostupný. Vývojáři muset programu jen pro spolehlivé rozhraní API kolekce a nechat spolehlivé kolekce Správa stavu replikují a místní.

Klíče rozdíl mezi spolehlivé kolekce a další technologie vysokou dostupnost (například Redis, služby Azure Table a fronty Azure service) je, že stav je uložen místně v instanci služby při také prováděné vysoce dostupný. To znamená, že:

* Všechny operace čtení jsou místní, výsledkem s nízkou latencí a vysokou propustností čte.
* Všech zápisů zpoplatněná minimální počet IOs sítě, což vede k s nízkou latencí a vysokou propustností zapisuje.

![Obrázek vývoj kolekcí.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Spolehlivé kolekce si lze představit jako přirozené vývoj **System.Collections** třídy: novou sadu kolekcí, které jsou určené pro cloudové a více počítači aplikace bez zvyšuje složitost pro vývojáře. Jako takový spolehlivé kolekce jsou:

* Replikované: Se pro zajištění vysoké dostupnosti replikují změny stavu.
* Trvalé: Data přetrvají na disk pro odolnost proti výpadkům ve velkém rozsahu (například výpadku napájení datacenter).
* Asynchronní: Rozhraní API jsou asynchronní zajistit, že nejsou při by docházelo k vstupně-výstupní operace zablokované vláken.
* Transakční: Rozhraní API využívat abstrakce transakce, můžete snadno spravovat více spolehlivé kolekcí v rámci služby.

Spolehlivé kolekce poskytují silnou konzistenci zaručuje předinstalované usnadnění reasoning o stavu aplikace.
Silnou konzistenci je dosaženo tím, že zajistí transakce, které potvrzení dokončit až poté, co je celá transakce byl zaprotokolován na většinu kvora repliky, včetně primární.
K dosažení slabší konzistence, může aplikace vědomí zpět do klienta žadatele o před vrátí asynchronního potvrzování.

Rozhraní API, spolehlivé kolekce jsou vývojem souběžných kolekcí rozhraní API (v nalezen **System.Collections.Concurrent** oboru názvů):

* Asynchronní: Vrátí úlohu, protože na rozdíl od souběžných kolekcí, jsou operace replikovat a trvalé.
* Žádná výstupní parametry: používá `ConditionalValue<T>` vrátí bool a hodnotu místo výstupní parametry. `ConditionalValue<T>` je třeba `Nullable<T>` ale nevyžaduje T být struktury.
* Transakce: Používá transakční objekt a umožňuje uživateli akce skupiny na více kolekcí spolehlivé v transakci.

V současné době **Microsoft.ServiceFabric.Data.Collections** obsahuje tři kolekce:

* [Spolehlivé slovník](https://msdn.microsoft.com/library/azure/dn971511.aspx): představuje replikovaných transakcí a asynchronní kolekci dvojic klíč/hodnota. Podobně jako **ConcurrentDictionary**, klíč a hodnotu můžou být jakéhokoli typu.
* [Spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx): představuje replikovaných transakcí a asynchronní striktní first-in použity fronty. Podobně jako **ConcurrentQueue**, hodnota může být jakéhokoli typu.
* [Spolehlivé souběžných fronty](service-fabric-reliable-services-reliable-concurrent-queue.md): představuje replikovaných transakcí a asynchronní úsilí nejlepší řazení fronty pro vysoké propustnosti. Podobně jako **ConcurrentQueue**, hodnota může být jakéhokoli typu.

## <a name="next-steps"></a>Další postup
* [Spolehlivé kolekce pokyny a doporučení](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky.](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Spolehlivá serializace kolekcí](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializace a Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuration Manager spolehlivé stavu](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
