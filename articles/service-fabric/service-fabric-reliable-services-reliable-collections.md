---
title: Úvod do spolehlivé kolekce v stavové služby Azure Service Fabric | Dokumentace Microsoftu
description: Service Fabric stavové služby poskytují spolehlivé kolekce, které umožňují také napsat vysoce dostupných, škálovatelných a nízkou latenci cloudových aplikací.
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
ms.date: 1/3/2019
ms.author: twhitney
ms.openlocfilehash: 422b4bbcfc6811cdc6bbf1649e2c660d04d95776
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039669"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Úvod do spolehlivé kolekce v stavové služby Azure Service Fabric

Reliable Collections umožňují zapisovat vysoce dostupných, škálovatelných a nízkou latenci cloudových aplikací, jako by byly psaní aplikací jeden počítač. Třídy v **Microsoft.ServiceFabric.Data.Collections** obor názvů poskytují sadu kolekcí, které automaticky nastavit svůj stav jako vysoce dostupný. Vývojáři potřebují pro aplikaci pouze pro spolehlivé kolekce rozhraní API a umožnit Reliable Collections Správa stavu replikují a místní.

Klíčovým rozdílem mezi Reliable Collections a dalších technologií vysoké dostupnosti (jako jsou Redis, službu Azure Table service a Azure Queue service) je, že stav je uložený místně v instanci služby při také prováděné s vysokou dostupností. To znamená, že:

* Všechny operace čtení jsou místní, což má za následek s nízkou latencí a vysokou propustnost čtení.
* Všechny operace zápisu se vám účtovat minimální počet síťových zařízení se systémy IOS, což vede k s nízkou latencí a vysokou propustnost zápisů.

![Obrázek vývoj kolekcí.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Spolehlivé kolekce si lze představit jako přirozeným vývojem **System.Collections** třídy: novou sadu kolekcí, které jsou navržené pro cloud a s více počítači aplikace bez zvýšení složitosti pro pro vývojáře. V důsledku toho spolehlivé kolekce jsou:

* Replikované: Změny stavu se replikují pro zajištění vysoké dostupnosti.
* Trvalý: Data se ukládají na disk pro odolnost proti výpadkům ve velkém měřítku (například k výpadku datového centra power).
* Vzhledem k tomu, že zápisy jsou trvale uložila a replikovala, nelze vytvořit volatile ReliableDictionary ReliableQueue či jiné spolehlivé kolekce, která udržuje pouze data v paměti.
* Asynchronní: Rozhraní API jsou asynchronní Ujistěte se, že nejsou při vstupně-výstupních operací za něj zablokované podprocesy.
* Transakční: Rozhraní API využívat abstrakce transakce, tak můžete snadno spravovat více spolehlivé kolekce v rámci služby.

Spolehlivé kolekce poskytují silnou konzistenci záruky mimo pole pro usnadnění uvažování o stavu aplikace.
Silná konzistence se dosahuje tím, že zajišťuje transakci dokončit potvrzení až po celou transakci byli přihlášeni kvorum replik, včetně primární.
Jak dosáhnout konzistence slabší, lze potvrdit aplikace zpět do klienta žadatele o dříve, než vrátí asynchronního potvrzování.

Rozhraní API pro spolehlivé kolekce jsou výsledkem vývoje souběžné kolekce rozhraní API (v nalezen **System.Collections.Concurrent** oboru názvů):

* Asynchronní: Vrátí úlohu, protože na rozdíl od souběžných kolekcí, operace replikuje a trvalé.
* Žádné výstupní parametry: Používá `ConditionalValue<T>` se vraťte `bool` a hodnotu místo výstupní parametry. `ConditionalValue<T>` je třeba `Nullable<T>` ale nevyžaduje T být struktura.
* Transakce: Objekt transaction využívá k zajištění uživatele do skupiny akcí v několika spolehlivé kolekce v transakci.

V současné době **Microsoft.ServiceFabric.Data.Collections** obsahuje tři kolekce:

* [Spolehlivého slovníku](https://msdn.microsoft.com/library/azure/dn971511.aspx): Představuje replikovaných transakcí a asynchronní kolekci dvojic klíč/hodnota. Podobně jako **ConcurrentDictionary**, klíč a hodnota může být libovolného typu.
* [Spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx): Představuje replikovaných transakcí a asynchronní striktní first-in FIFO (FIFO) fronty. Podobně jako **ConcurrentQueue**, hodnota může být libovolného typu.
* [Spolehlivá souběžná fronta](service-fabric-reliable-services-reliable-concurrent-queue.md): Představuje replikovaných transakcí a asynchronní nezaručené řazení fronty pro vysokou propustnost. Podobně jako **ConcurrentQueue**, hodnota může být libovolného typu.

## <a name="next-steps"></a>Další postup

* [Pokyny a doporučení Reliable Collection](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakce a zámky](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Správa dat
  * [Zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)
  * [Oznámení](service-fabric-reliable-services-notifications.md)
  * [Spolehlivá serializace kolekcí](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializace a Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurace Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Ostatní
  * [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
