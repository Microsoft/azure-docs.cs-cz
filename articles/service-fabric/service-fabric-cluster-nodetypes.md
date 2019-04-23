---
title: Typy uzlů Service Fabric a Azure virtuálního počítače škálovací sady | Dokumentace Microsoftu
description: Zjistěte, jak sady uzlů Azure Service Fabric, které se týkají typů škálování virtuálního počítače a jak pro vzdálené připojení ke škálovací sadu instance nebo uzel clusteru.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386095"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typy uzlů Service Fabric a Azure virtuálního počítače škálovací sady
[Škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets) jsou tedy výpočetní prostředek Azure. Škálovací sady můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric nastaví samostatné škálování.  Modul runtime Service Fabric nainstalovaný na každém virtuálním počítači ve škálovací nastavený. Můžete nezávisle na sobě horizontální i vertikální škálování každého typu uzlu, změnit skladová položka operačního systému, které běží na všech uzlech clusteru, mají různé sady otevřených portů a použít různé metriky kapacity.

Následující obrázek znázorňuje cluster, který má dva typy uzlů s názvem front-endových a back-endu. Každý typ uzlu s pěti uzly.

![Cluster, který má dva typy uzlů][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapování instancí škálovací sady virtuálních počítačů do uzlů
Jak je znázorněno na předchozím obrázku, instancí škálovací sady spuštění na instanci 0 a poté zvýšit o 1. Číslování se projeví v uzlu názvy. Například uzel BackEnd_0 je 0 instance škálovací sady back-endu. Tento konkrétní škálovací sady má pět instancí, s názvem BackEnd_0 BackEnd_1, BackEnd_2, BackEnd_3 a BackEnd_4.

Když vertikálně navýšit kapacitu škálovací sady, je vytvořena nová instance. Nový název instance škálovací sady je obvykle škálovací sady názvu plus číslo další instance. V našem příkladu je BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapování nástroje pro vyrovnávání zatížení škálovací sady na typy uzlů a škálovací sady
Pokud váš cluster na webu Azure Portal nasadit nebo použít ukázkové šablony Azure Resource Manageru, jsou uvedeny všechny prostředky ve skupině prostředků. Můžete zobrazit nástroje pro vyrovnávání zatížení pro každou škálovací sady nebo uzel typu. Název nástroje pro vyrovnávání zatížení používá následující formát: **LB -&lt;název typu uzlu&gt;**. Příkladem je LB-sfcluster4doc-0, jak je znázorněno na následujícím obrázku:

![Zdroje a prostředky][Resources]


## <a name="next-steps"></a>Další postup
* Zobrazit [Přehled funkce "Nasazení kdekoli" a porovnání s využitím clusterů Azure managed](service-fabric-deploy-anywhere.md).
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Vzdálené připojení](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) k instanci konkrétní škálovací sady
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) v clusteru virtuální počítače po nasazení
* [Změnit uživatelské jméno admin a heslo](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
