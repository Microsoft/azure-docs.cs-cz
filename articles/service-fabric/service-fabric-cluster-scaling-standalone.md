---
title: Škálování samostatného clusteru v Azure Service Fabric
description: Přečtěte si, jak škálovat nebo vypínat samostatné clustery, nebo jejich Service Fabric škálování nebo snížení kapacity.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843027"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Škálování Service Fabric samostatných clusterů
Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery můžou obsahovat potenciálně tisíce uzlů. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo vertikálně (změnit prostředky uzlů).  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

Proč škálovat cluster? Aplikace vyžaduje změnu v průběhu času.  Možná budete muset zvýšit prostředky clusteru tak, aby splňovaly zvýšené zatížení aplikace nebo síťový provoz nebo snížili prostředky clusteru, když na vyžádání klesne požadavek.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Horizontální navýšení a zmenšení nebo horizontální škálování
Změní počet uzlů v clusteru.  Jakmile se nové uzly připojí ke clusteru, Správce prostředků k nim [cluster](service-fabric-cluster-resource-manager-introduction.md) přesune služby, které snižují zatížení stávajících uzlů.  Můžete také snížit počet uzlů, pokud se prostředky clusteru nepoužívají efektivně.  Když uzly opustí cluster, služby se z těchto uzlů pohybují a zatížení zbývajících uzlů se zvýší.  Snížení počtu uzlů v clusteru spuštěném v Azure vám může ušetřit peníze, protože platíte za počet virtuálních počítačů, které používáte, a ne na těchto virtuálních počítačích.  

- Výhody: nekonečná škála teoreticky.  Pokud je vaše aplikace navržena pro škálovatelnost, můžete povolit bez omezení nárůst přidáním dalších uzlů.  Nástroje v cloudovém prostředí usnadňují přidávání a odebírání uzlů, takže je možné snadno upravit kapacitu a platíte jenom za prostředky, které využijete.  
- Nevýhody: aplikace musí být [navržené pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikační databáze a trvalosti mohou vyžadovat i další strukturální práci pro škálování.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) v Service Fabric stavové služby, ale výrazně usnadňují škálování dat aplikací.

Samostatné clustery umožňují nasazení Service Fabric clusteru v místním prostředí nebo ve vámi zvoleném poskytovateli cloudu.  Typy uzlů se skládají z fyzických počítačů nebo virtuálních počítačů v závislosti na vašem nasazení. Ve srovnání s clustery, které běží v Azure, se proces škálování samostatného clusteru trochu nezabývá.  Musíte ručně změnit počet uzlů v clusteru a pak spustit upgrade konfigurace clusteru.

Odebrání uzlů může iniciovat více upgradů. Některé uzly jsou označeny `IsSeedNode=”true”` značkou a lze je identifikovat pomocí dotazu na manifest clusteru pomocí příkazu [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Odebrání takových uzlů může trvat déle než jiné, protože počáteční uzly se v takových scénářích musí pohybovat. Cluster musí udržovat minimálně tři uzly typu primární uzel.

> [!WARNING]
> Doporučujeme, abyste u clusteru nesnížili počet uzlů pod [Velikost clusteru úrovně spolehlivosti](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) . To bude mít vliv na schopnost Service Fabric systémových služeb se replikovat napříč clusterem a pak bude cluster destabilizovat nebo může zničit.
>

Při škálování samostatného clusteru mějte na paměti následující pokyny:
- Nahrazení primárních uzlů by mělo být provedeno v jednom uzlu za jiným uzlem namísto odebrání a následně přidání v dávkách.
- Před odebráním typu uzlu ověřte, zda existují uzly odkazující na typ uzlu. Odeberte tyto uzly před odebráním odpovídajícího typu uzlu. Po odebrání všech odpovídajících uzlů můžete z konfigurace clusteru odebrat uzel NodeType a zahájit upgrade konfigurace pomocí funkce [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Další informace najdete v tématu [škálování samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Vertikální škálování a svislé škálování 
Změní prostředky (CPU, paměť nebo úložiště) uzlů v clusteru.
- Výhody: architektura softwaru a aplikací zůstává stejná.
- Nevýhody: omezené škálování, protože existuje omezení, kolik můžete zvýšit množství prostředků na jednotlivých uzlech. Výpadky, protože budete muset přebírat fyzické nebo virtuální počítače offline, aby bylo možné přidat nebo odebrat prostředky.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)

