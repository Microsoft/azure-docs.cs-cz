---
title: Azure Service Fabric typů uzlů a virtuálního počítače sady škálování | Microsoft Docs
description: Zjistěte, jak nastaví uzlu Azure Service Fabric, které se týkají typů škálování virtuálních počítačů, a jak se vzdáleně připojit ke stupnici nastavit instanci nebo uzlu clusteru.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: ce3b3e6f3c7ee0c7198c27ed0ca0e3610d26f143
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric typů uzlů a virtuálního počítače sady škálování
[Sady škálování virtuálního počítače](/azure/virtual-machine-scale-sets) jsou Azure výpočetní prostředky. Můžete nasazovat a spravovat kolekci jako sada virtuálních počítačů sady škálování. Každý typ uzlu, který definujete v clusteru služby Azure Service Fabric nastaví samostatné škálování.  Nastavit modulu runtime Service Fabric, které jsou nainstalované na každém virtuálním počítači v měřítka. Můžete nezávisle škálovat každý typ uzlu nahoru nebo dolů, změňte skladová položka operačního systému, spuštěná v každém uzlu clusteru, mají různé sady otevřené porty a použít jiný kapacity metriky.

Následující obrázek znázorňuje clusteru, který má dva typy uzel s názvem front-endové a back-end. Každý typ uzlu s pěti uzly.

![Cluster, který má dva typy uzlů][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapování instancí sady škálování virtuálního počítače pro uzly
Jak je vidět na předchozím obrázku, instancí sady škálování spuštění na instanci 0 a poté zvýšit 1. Číslování se projeví v názvech uzlu. Například uzel BackEnd_0 je 0 instance škálovací sady back-end. Tato sada konkrétní škálování má pět instancí s názvem BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 a BackEnd_4.

Při změně měřítka si sadu škálování, je vytvořena nová instance. Název instance nové sady škálování je obvykle že měřítka nastavit název plus číslo další instance. V našem příkladu je BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Měřítko mapy typy uzlů nastavte nástroje pro vyrovnávání zatížení a škálování sady
Pokud nasadíte cluster v portálu Azure nebo použít šablony Azure Resource Manageru ukázkový, jsou uvedeny všechny prostředky ve skupině prostředků. Můžete zobrazit nástroje pro vyrovnávání zatížení pro každý typ uzel nebo sada škálování. Název služby Vyrovnávání zatížení používá následující formát: **LB -&lt;název typu uzlu&gt;**. Příkladem je LB-sfcluster4doc-0, jak je znázorněno na následujícím obrázku:

![Zdroje a prostředky][Resources]


## <a name="next-steps"></a>Další postup
* Najdete v článku [Přehled funkce "Nasazení odkudkoli" a porovnání s Azure spravované clustery](service-fabric-deploy-anywhere.md).
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Vzdálené připojení](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) do instance sady konkrétní škálování
* [Aktualizujte hodnoty rozsahu portu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) v clusteru virtuálních počítačů po nasazení
* [Změňte uživatelské jméno správce a heslo](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro cluster virtuálních počítačů

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
