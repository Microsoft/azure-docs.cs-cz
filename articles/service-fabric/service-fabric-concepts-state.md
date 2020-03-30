---
title: Správa stavu ve službách Azure Service Fabric
description: Další informace o stavu v Azure Service Fabric, včetně toho, jak definovat a spravovat stav služby ve službách Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614566"
---
# <a name="service-state"></a>Stav služby
**Stav služby** odkazuje na data v paměti nebo na disku, která služba vyžaduje ke funkci. Zahrnuje například datové struktury a členské proměnné, které služba čte a zapisuje do práce. V závislosti na tom, jak je služba navržena, může také obsahovat soubory nebo jiné prostředky, které jsou uloženy na disku. Například soubory databáze by použít k ukládání dat a protokoly transakcí.

Jako příklad služby zvažme kalkulačku. Základní kalkulačka služba trvá dvě čísla a vrátí jejich součet. Provedení tohoto výpočtu nezahrnuje žádné členské proměnné nebo jiné informace.

Nyní zvažte stejnou kalkulačku, ale s další metodou pro ukládání a vrácení posledního součtu, který vypočítal. Tato služba je nyní stavové. Stavové znamená, že obsahuje některé stav, který zapíše, když vypočítá nový součet a čte z při dotazu, že vrátí poslední vypočítaný součet.

Ve službě Azure Fabric první služba se nazývá bezstavová služba. Druhá služba se nazývá stavová služba.

## <a name="storing-service-state"></a>Ukládání stavu služby
Stav může být externalizovánnebo umístěn společně s kódem, který manipuluje se stavem. Externalizace stavu se obvykle provádí pomocí externí databáze nebo jiného úložiště dat, které běží na různých počítačích v síti nebo mimo proces ve stejném počítači. V našem příkladu kalkulačky může být úložiště dat databáze SQL nebo instance Azure Table Store. Každý požadavek na výpočet součtu provádí aktualizaci těchto dat a požadavky na službu vrátit výsledek hodnoty v aktuální hodnotu načtenou z úložiště. 

Stav může být také umístěn společně s kódem, který manipuluje se stavem. Stavové služby v Service Fabric jsou obvykle sestaveny pomocí tohoto modelu. Service Fabric poskytuje infrastrukturu k zajištění, že tento stav je vysoce dostupné, konzistentní a trvanlivé a že služby vytvořené tímto způsobem lze snadno škálovat.

## <a name="next-steps"></a>Další kroky
Další informace o konceptech service fabric naleznete v následujících článcích:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric](service-fabric-concepts-scalability.md)
* [Služby partitioning Service Fabric](service-fabric-concepts-partitioning.md)
* [Spolehlivé služby Service Fabric](service-fabric-reliable-services-introduction.md)
