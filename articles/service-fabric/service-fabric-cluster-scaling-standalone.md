---
title: Škálování clusterů samostatné s Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o škálování samostatných clusterů Service Fabric v nebo navýšení kapacity a nahoru nebo dolů.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: 0c211c2bb9dc07e705679b5d4079b85de9d72d8c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100457"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Škálování samostatných clusterů Service Fabric
Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery můžou potenciálně obsahovat tisících uzlech. Po vytvoření clusteru Service Fabric, je možné škálovat cluster vodorovně (změnit počet uzlů), nebo svisle (změnit prostředky uzly).  Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.  Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

Proč škálování clusteru? Požadavky na aplikace v průběhu času měnit.  Budete muset zvýšit prostředky clusteru k zajištění provozu aplikace vyšší zatížení nebo sítě nebo omezovat prostředky clusteru, jakmile poptávka poklesne.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dovnitř a ven vertikální nebo horizontální škálování
Změní z počtu uzlů v clusteru.  Jakmile nových uzlů připojit ke clusteru, [Cluster Resource Manageru](service-fabric-cluster-resource-manager-introduction.md) přesune služby k nim, což snižuje zátěž existujících uzlů.  Pokud prostředky clusteru nejsou využívány efektivně, může snížit počet uzlů.  Nechat uzly clusteru, služby přestali ty uzly a zvyšuje na zbývajících uzlech zátěž.  Snížení počtu uzlů v clusteru běžícího v Azure dají ušetřit peníze, protože platíte za počet virtuálních počítačů, které není úloha v těchto virtuálních počítačů a použití.  

- Výhody: Nekonečné škálování teoreticky.  Pokud vaše aplikace je určená pro škálovatelnost, můžete povolit neomezené růstu přidáním více uzlů.  Nástrojů v prostředí cloudu umožňuje snadno přidávat a odebírat uzly, tak snadno upravit kapacitu a platíte jenom za prostředky, které používáte.  
- Nevýhody: Aplikace musí být [určená pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikačních databází a trvalost můžou vyžadovat další architektury práce jako správné škálování.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) v Service Fabric stavové služby, ale to značně zjednodušují škálování dat aplikací.

Samostatné clustery umožňují nasazení Service Fabric cluster místně nebo v poskytovateli cloudu podle vašeho výběru.  Typy uzlů se skládají z fyzických strojích nebo virtuálních počítačů, v závislosti na nasazení. Ve srovnání s clustery v Azure, proces škálování samostatného clusteru se zapojí o něco víc.  Musíte ručně změnit počet uzlů v clusteru a pak spusťte konfigurace upgradu clusteru.

Odebrání uzlů může zahájit více upgradů. Některé uzly jsou označené `IsSeedNode=”true”` značku a lze je identifikovat pomocí dotazu na clusteru manifestu pomocí [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Odebrání těchto uzlů může trvat déle než jiné, jelikož budou muset počáteční uzly přesouvat v takových scénářích. Cluster, musíte mít minimálně tří uzlů typ primárního uzlu.

> [!WARNING]
> Doporučujeme, abyste nesníží níže počet uzlů [velikost clusteru úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) pro cluster. Bude to narušit schopnost systémové služby Service Fabric replikuje v clusteru a bude destabilizovat nebo případně odstranit cluster.
>

Při škálování samostatného clusteru, mějte podle následujících pokynů:
- Nahrazení hlavní uzly, které musí být jeden uzel provádí po druhé, namísto odeberte a pak přidejte v dávkách.
- Před odebráním typ uzlu, zkontrolujte, jestli jsou všechny uzly, které odkazuje typ uzlu. Před odebráním odpovídající typ uzlu, odeberte tyto uzly. Jakmile se odeberou všechny odpovídající uzly, můžete odebrat uzlu (NodeType) z konfigurace clusteru a začněte konfiguraci upgrade s využitím [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Další informace najdete v tématu [škálování samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Vertikální navýšení nebo snížení kapacity nebo vertikální škálování 
Změní prostředky (procesor, paměť nebo úložiště) z uzlů v clusteru.
- Výhody: Architektura softwaru a aplikace zůstala stejná.
- Nevýhody: Konečná škálování, protože neexistuje omezení můžete zvýšit množství prostředky v jednotlivých uzlech. Výpadky, protože budete muset provést fyzických nebo virtuálních počítačů do režimu offline za účelem přidání nebo odebrání prostředků.

## <a name="next-steps"></a>Další postup
* Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure snížení nebo navýšení kapacity](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure compute SDK.
* [Horizontální snížení nebo navýšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md).

