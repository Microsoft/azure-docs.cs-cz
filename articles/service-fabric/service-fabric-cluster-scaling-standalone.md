---
title: Škálování samostatného clusteru Azure Service Fabric
description: Další informace o škálování service fabric samostatné clustery dovnitř nebo ven a nahoru nebo dolů.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451913"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Samostatné clustery infrastruktury služby Škálování
Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery mohou obsahovat potenciálně tisíce uzlů. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo svisle (změnit prostředky uzlů).  Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.  Při škálování clusteru se automaticky škálují také vaše aplikace.

Proč škálovat cluster? Požadavky aplikací se v průběhu času mění.  Možná budete muset zvýšit prostředky clusteru, aby bylo možné splnit zvýšené zatížení aplikací nebo síťový provoz nebo snížit prostředky clusteru při poklesu poptávky.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Změna velikosti a zvětšování velikosti nebo vodorovné škálování
Změní počet uzlů v clusteru.  Jakmile se nové uzly připojí ke clusteru, [Správce prostředků clusteru](service-fabric-cluster-resource-manager-introduction.md) přesune do nich služby, které snižují zatížení existujících uzlů.  Můžete také snížit počet uzlů, pokud prostředky clusteru nejsou používány efektivně.  Jako uzly opustit clusteru, služby přesunout z těchto uzlů a zvýšení zatížení na zbývající uzly.  Snížení počtu uzlů v clusteru spuštěném v Azure vám může ušetřit peníze, protože platíte za počet virtuálních počítačů, které používáte, a ne za zatížení těchto virtuálních počítačů.  

- Výhody: Nekonečné měřítko, teoreticky.  Pokud je vaše aplikace určena pro škálovatelnost, můžete povolit neomezený růst přidáním dalších uzlů.  Nástroje v cloudových prostředích usnadňují přidávání nebo odevzdání uzlů, takže je snadné upravit kapacitu a platit jenom za prostředky, které používáte.  
- Nevýhody: Aplikace musí být [navrženy pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikační databáze a trvalost může vyžadovat další architektonické práce škálovat také.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) ve stavových službách Service Fabric však usnadňují škálování dat aplikací.

Samostatné clustery umožňují nasadit cluster Service Fabric místně nebo u poskytovatele cloudu podle vašeho výběru.  Typy uzlů se skládají z fyzických počítačů nebo virtuálních počítačů, v závislosti na nasazení. Ve srovnání s clustery spuštěné v Azure je proces škálování samostatného clusteru o něco více.  Je nutné ručně změnit počet uzlů v clusteru a potom spustit upgrade konfigurace clusteru.

Odebrání uzlů může zahájit více inovací. Některé uzly `IsSeedNode=”true”` jsou označeny značkou a lze je identifikovat dotazováním na manifest clusteru pomocí [get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Odebrání těchto uzlů může trvat déle než ostatní, protože uzly osiva budou muset být v takových scénářích přesunuty. Cluster musí udržovat minimálně tři uzly typu primárního uzlu.

> [!WARNING]
> Doporučujeme, abyste nesnižovali počet uzlů pod [velikost clusteru úrovně spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) pro cluster. To bude na rušit schopnost service fabric systémové služby replikovat v celém clusteru a destabilizuje nebo případně zničit clusteru.
>

Při škálování samostatného clusteru mějte na paměti následující pokyny:
- Nahrazení primárních uzlů by mělo být provedeno jeden uzel za druhým, namísto odebrání a následného přidání v dávkách.
- Před odebráním typu uzlu zkontrolujte, zda existují nějaké uzly odkazující na typ uzlu. Před odebráním odpovídajícího typu uzlu odeberte tyto uzly. Po odebrání všech odpovídajících uzlů můžete odebrat typ NodeType z konfigurace clusteru a zahájit upgrade konfigurace pomocí [programu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Další informace naleznete v [tématu škálování samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Změna velikosti nahoru a dolů nebo vertikální škálování 
Změní prostředky (procesor, paměť nebo úložiště) uzlů v clusteru.
- Výhody: Architektura softwaru a aplikací zůstává stejná.
- Nevýhody: Konečné měřítko, protože existuje limit, o kolik můžete zvýšit prostředky na jednotlivých uzlech. Prostoje, protože budete muset převést fyzické nebo virtuální počítače do režimu offline, abyste mohli přidat nebo odebrat prostředky.

## <a name="next-steps"></a>Další kroky
* Informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure dovnitř nebo ven](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure programově](service-fabric-cluster-programmatic-scaling.md) pomocí plynulé Azure compute SDK.
* [Škálování samostatného clusteru dovnitř nebo ven](service-fabric-cluster-windows-server-add-remove-nodes.md).

