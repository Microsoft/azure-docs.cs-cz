---
title: Správa stavu ve službě Azure Service Fabric Services
description: Přečtěte si o stavu ve službě Azure Service Fabric, včetně toho, jak v Service Fabricch službách definovat a spravovat stav služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614566"
---
# <a name="service-state"></a>Stav služby
**Stav služby** odkazuje na data v paměti nebo na disku, která služba vyžaduje, aby fungovala. Obsahuje například datové struktury a proměnné členů, které služba načítá a zapisuje, aby fungovala. V závislosti na tom, jak je služba navržená, může taky zahrnovat soubory nebo jiné prostředky, které jsou uložené na disku. Například soubory, které databáze používá k ukládání dat a protokolů transakcí.

Jako příklad služby bereme v úvahu kalkulačku. Základní služba kalkulačky používá dvě čísla a vrací jejich součet. Provádění tohoto výpočtu nevyžaduje žádné členské proměnné ani jiné informace.

Nyní uvažujte o stejné kalkulačkě, ale s další metodou pro uložení a vrácení posledního součtu, který vypočítal. Tato služba je nyní stavová. Stav znamená, že obsahuje určitý stav, do kterého se zapisuje při výpočtu nového součtu a čtení z, když požádáte, aby vrátila poslední vypočítaný součet.

V Azure Service Fabric se první služba nazývá Bezstavová služba. Druhá služba se nazývá stavová služba.

## <a name="storing-service-state"></a>Ukládá se stav služby
Stav může být buď externě, nebo společně umístěný s kódem, který zpracovává stav. Externing State se obvykle provádí pomocí externí databáze nebo jiného úložiště dat, které běží na různých počítačích v síti nebo mimo proces ve stejném počítači. V našem příkladu kalkulačky může být úložiště dat databáze SQL nebo instance Azure Table Storu. Každý požadavek na výpočet součtu provede aktualizaci těchto dat a požadavky na službu vrátí výsledek v aktuální hodnotě, která se načítá ze Storu. 

Stav může být také umístěn společně s kódem, který zpracovává stav. Stavové služby v Service Fabric jsou obvykle sestavené pomocí tohoto modelu. Service Fabric poskytuje infrastrukturu, která zajistí, že je tento stav vysoce dostupný, konzistentní a trvalý a že služby vytvořené tímto způsobem je možné snadno škálovat.

## <a name="next-steps"></a>Další kroky
Další informace o Service Fabric konceptech najdete v následujících článcích:

* [Dostupnost služeb Service Fabric Services](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric Services](service-fabric-concepts-scalability.md)
* [Dělení Service Fabric služeb](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
